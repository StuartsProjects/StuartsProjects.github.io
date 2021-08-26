---
layout: post
title: "Reliable Packets with LoRa - Part 2"
date: "2021-08-26"
---

## Sending and receiving reliable packets with an acknowledge

Sometimes we may need to be sure the receiver has actually received the packet sent and we may want to keep transmitting the packet until it actually is received. Thus the receiver needs, when it has accepted a valid reliable packet, to send an acknowledge packet back that the transmitter will be listening for. 

Clearly we could now have the same problem as for a simple reliable packet, how does the transmitter know the received packet actually is an acknowledge from the specific receiver ? 

The simple way to be sure is to turn around the received NetworkID and payloadcrc (i.e. a total of 4 bytes) and send these bytes as an acknowledge. The transmitter program knows the NetworkID and payloadcrc used in the transmit function so can check to a high level of certainty that the received packet is a valid acknowledge, all four bytes of the acknowledge have to be correct. 


## Demonstration examples using acknowledgements

The previous examples, where there was no acknowledgement used were a very basic transmit and receive, a set of examples using a structure to control an remote output, doing the same with direct array read and write and then a low memory example writing and reading direct to the LoRa devices buffer. There are two types of acknowledgement possible with the library, the first is a simple Auto Acknowledge which is only 4 bytes, consisting of the the NetworkID and payload CRC. The second type of acknowledge allows the receiver to send back data to the transmitter. This form of acknowledge can be used when the transmitter wants to request some data or control information from the receiver. Since the original NetworkID and payload CRC used by the transmitter is sent back with the acknowledge the transmitter can be very confident that the data coming back is genuine. 

These two examples; **209\_Reliable\_Transmitter\_AutoACK** and **210\_Reliable\_Receiver\_AutoACK** are the basic 201 and 202 examples modified to use the auto acknowledge. 

The transmit function when acknowledge is configured is;

	transmitReliable(buff, sizeof(buff), NetworkID, ACKtimeout, TXtimeout, TXpower, WAIT_TX))

If AutoACK is used then a ACKtimeout needs to be specified. ACKtimeout is the milliseconds the transmit function would listen for a valid acknowledge before returning an error. How short this period is rather depends on the LoRa settings in use and the actual Arduinos being used. Remember the sending of the acknowledge will have a on-air time that the transmitter needs to account for. You can in some circumstances have an ACKtimeout as low as 25mS and that is still enough time for the receiver to turn around from receive to transmit and the transmitter to flip to receive mode and pick-up the packet. You need to experiment here, perhaps start at 1000mS and gradually reduce the time (with a working set-up) until the point is reached when the receipt of the acknowledge fails.  

With the transmit function if the returned byte is 0 this indicates to the sketch that there has been an error of some type, one such error could be no acknowledge received. 

The matching receive function is;

	receiveReliable(RXBUFFER, RXBUFFER_SIZE, NetworkID, ACKdelay, TXpower, RXtimeout, WAIT_RX);

The ACKdelay parameter is in miliseconds and it's the time the receiver waits before sending an acknowledge. With some hardware a delay here of 0mS might be OK, but with faster hardware you may need to increase it. Maybe start with an ACKdelay of 50mS and an ACKtimeout of 1000mS in the transmit function and reduce the numbers in steps. You only need to do this if you want or need to maximise response time. 

If the returned byte from the receiveReliable() function is 0 then there was a problem during receive. 

The 209 transmitter will keep sending the payload until the transmitReliable() function returns a non zero value. The transmitReliable() function will return a zero value if no acknowledge is received within the ACKtimeout period. 

Note that in the case of a NetworkID mismatch the receiver will not transmit an acknowledge, so the transmitter reports it as an a NoReliableACK error.
	
The auto acknowledge is a simple way of making the transmission of packets more reliable, but it might not be appropriate in all circumstances. For instance consider the **207\_Reliable\_Transmitter\_Controller\_LowMemory** and **208\_Reliable\_Receiver\_Controller\_LowMemory** examples where the payload contains the following;

	LT.writeUint16(destinationNode);                   //destination node for packet 
	LT.writeUint8(outputNumber);                       //output number on receiver
	LT.writeUint8(onoroff);                            //0 for off, 1 for on

Here the destinationNode number is directed to a particular node number, 2 in that example. If the packet is received by node number 5, then there should be no acknowledge sent. In these circumstances the receiver program needs to intervene directly on the received packet, read the payload and check for a matching destinationNode number. If there is a match then an acknowledge can be sent manually and the transmitter knows the packet has been received. 

## Low memory auto acknowledge 

Example programs **211\_Reliable\_SXTransmitter\_AutoACK** and **212\_Reliable\_SXReceiver\_AutoACK** demonstrate how to use the SX12xx library functions to bypass the need to use an intermediate array for the payload. Instead of filling an array with a structure or byte array full of variables, the variables to send are written directly to the LoRa device internal buffer on transmit and read from the buffer on receive. 

Filling the SX12XX LoRa devices buffer is done like so;

	LT.startWriteSXBuffer(0);                     //start the write at SX12XX internal buffer location 0
    LT.writeUint16(destinationNode);              //destination node for packet
    LT.writeUint8(outputNumber);                  //output number on receiver
    LT.writeUint8(onoroff);                       //0 for off, 1 for on
    TXPayloadL = LT.endWriteSXBuffer();           //closes packet write and returns the length of the payload to send
 
And then the appropriate transmit function is used;

	TXPacketL = LT.transmitSXReliableAutoACK(0, TXPayloadL, NetworkID, ACKtimeout, TXtimeout, TXpower, WAIT_TX);  

On the receive side the receive is setup like so;

	PacketOK = LT.receiveSXReliableAutoACK(0, NetworkID, ACKdelay, TXpower, RXtimeout, WAIT_RX);

And if on receive the packet passes the NetworkID and payloadCRC checks (PacketOK returns > 0) it is read like this;

	LT.startReadSXBuffer(0);                       //start buffer read at location 0
  	destinationNode = LT.readUint16();             //load the destination node
  	outputNumber = LT.readUint8();                 //load the output number
  	onoroff = LT.readUint8();                      //0 for off, 1 for on
  	RXPayloadL = LT.endReadSXBuffer();             //this function returns the length of the array read

Note that in this example the payload contains a 16 bit destinationNode variable, which can be used to direct the packet to one of many nodes. The sketch checks that the destinationNode matches the number given to that receiver and if there is a match actions the packet. 


## Manual acknowledge

The programs **213\_Reliable\_Transmitter\_Controller\_ManualACK** and **214\_Reliable\_Receiver\_Controller\_ManualACK** use a manual acknowledge set-up whereby the receive picks up the transmitted payload and reads the destinationNode parameter to decide if the packet is destined for that node. If it is the acknowledge is sent which contains the networkID and the CRC of the original payload, thus the transmitter knows the sent packet has been received correctly. 

The receive sketch can pause at the point the payload is being actioned, perhaps reading an external sensor waiting for conformation that the action as completed, a gate is confirmed opened\closed for instance, before sending the acknowledge. 

There is a further enhancement to the manual acknowledge set-up, the acknowledge can contain some data to be returned to the transmitter. 

## Manual acknowledge returning data

The standard acknowledge is only 4 bytes, the NetworkID and payload CRC. However the acknowledge can be sent with an array of data included in the acknowledge.

The format of this function is;

	transmitReliableACK(uint8\_t *txbuffer, uint8\_t size, uint16\_t networkID, uint16\_t payloadcrc, int8\_t txpower);  

Here the receiver sending the acknowledge can include an array txbuffer of a specified size. This returned array could be a structure, as per example 203 and 204 or an array filled directly with the arrayRW.h library file as used in examples 205 and 206.

To demonstrate returning an array in the acknowledge examples 201 and 202 were modified so that the transmitted 'Hello World' example has 'Goodbye' returned from the receiver with the acknowledge and is then printed out on the transmitter. The modified programs are **215\_Reliable\_Transmitter\_ManualACK\_withData** and **216\_Reliable\_Receiver\_ManualACK\_withData**


## Requesting data

We can use the acknowledge functions of reliable packets to send a request to a remote station to transfer data. The returned data will include the networkID and payloadCRC sent with the request and since the returned data\packet is also protected by the internal LoRa packet CRC we can be fairly confident we are getting valid data back. 

Take for instance a remote node that has a GPS attached and we want to know the current location of the node. Of course the node could transmit its location on a regular basis but this can be wasteful of power and there will be issues with conflicting transmissions if there are a number of nodes out there. 

Examples **217\_Reliable\_Transmitter\_Data\_Requestor** and **218\_Reliable\_Receiver\_Data\_Requestor** demonstrate such a requesting of data.

Assume at the station wanting the remote nodes location we send this payload;

	LT.startWriteSXBuffer(0);                   //initialise SX buffer write at address 0
    LT.writeUint8(RequestGPSLocation);          //identify type of packet
    LT.writeUint8(RequestStation);              //station to reply to request
    TXPayloadL = LT.endWriteSXBuffer();         //close SX buffer write 

RequestGPSLocation is assigned a value of 1;

	const uint8_t RequestGPSLocation = 1;

RequestStation is from 0 to 255 and is the station node\number that we want the location from, in this case RequestStation will have a number of 123. 

The packet is protected by the NetworkID and payload CRC so station 123 will see the request as valid and loads the location data into the LoRa device like this;

	LT.startWriteSXBuffer(0);                   //initialise SX buffer write at address 0
    LT.writeUint8(RequestGPSLocation);          //identify type of request
    LT.writeUint8(ThisStation);                 //who is the request reply from
    LT.writeFloat(TestLatitude);                //add latitude
    LT.writeFloat(TestLongitude);               //add longitude
    LT.writeFloat(TestAltitude);                //add altitude
    TXPayloadL = LT.endWriteSXBuffer();         //close SX buffer write

In this case the returned values are test values of Latitude, Longitude and Altitude. 

The original requesting node sees that the valid reply\Acknowledge is a GPS location request from station 123 and can then act on the data.





## Using program 220\_LoRa\_Packet\_Monitor

When debugging what's going on in a send and acknowledge set-up its useful to be able to see what is happening in real time. This packet monitor example will display the bytes received in hexadecimal, in the example printout below you can see two packets. The 16 byte packet contains the text 'Hello World' and then the NetworkID, 0x3210, then the payload CRC, 0xBC69 at the end. 

The 4 byte packet that is seen around 130mS later is the acknowledge which contains the NetworkID, 0x3210, then the payload CRC, 0xBC69. 


	125.103 RSSI,-99dBm,SNR,10dB  16 bytes > 48 65 6C 6C 6F 20 57 6F 72 6C 64 00 10 32 69 BC 
	125.237 RSSI,-96dBm,SNR,8dB  4 bytes > 10 32 69 BC 




## Stuart Robinson
## August 2021