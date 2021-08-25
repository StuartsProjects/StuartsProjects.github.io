---
layout: post
title: "Reliable Packets with LoRa"
date: "2021-08-25"
---


## Reliable Packets

It often seems to be assumed that if you transmit a LoRa packet then that packet will be received correctly and the receiver can also assume the received packet is valid and OK to use. There are several reasons why this is not a correct assumption.

Obviously corrupt packets with a failed CRC can be rejected, but you do need do to be sure the LoRa devices internal CRC is actually enabled, by default a particular library may not enable it. If its not enabled, a corrupt packet can be accepted. 

The receiver could also be picking up packets from another transmitter and given the very long range nature of LoRa this other transmitter could be many kilometres away so you may have no idea it's there. In a dense urban or industrial area what are the chances that there are other LoRa transmitters within range sending out packets that you would rather not receive ? 

Another issue; if you send a packet and it's purpose is to remotely control some, possibly important, external device how can you be really sure the receiver has actually received the transmitted packet and has also actually carried out the intended action ?

### Phantom packets

There is yet another issue, LoRa receivers can generate 'phantom' packets internally, this is discussed at length here;

[A LoRa Mystery – Phantom Packets](https://stuartsprojects.github.io/2018/05/29/a-LoRa-mystery-phantom-packets.html)

[A LoRa mystery – Phantom packets but deeper](https://stuartsprojects.github.io/2018/05/31/a-LoRa-mystery-phantom-packets-but-deeper.html)

[Phantom Packets – Is this the reason ?](https://stuartsprojects.github.io/2018/06/02/phantom-packets-is-this-the-reason.html)

The issue with phantom packets is that just because the LoRa packet CRC error flag is not set, it does not mean the packet passed a CRC check. If the packet header (possibly corrupt) indicates that there is no CRC for the payload then no check is done and the error flag cannot be set. Unfortunately some of the common Arduino libraries do not address this issue at all. These 'phantom' packets just contain random data.

### Foreign Packets

The internal CRC checking used by LoRa devices for transmission and reception will not help you to eliminate a received packet that has arrived from a foreign or rogue source. Thus you could be allowing corrupted data into an application or erroneously controlling some external devices, which may not be good.

### The need to be sure

In summary it would be helpful, and in some cases essential, if you could be really (+really +really +really) sure that a received packet was actually destined for that particular receiver and that it also contained valid sensor or control data for the application the receiver is running.

These issues are what got me thinking about how to make packet transmission and reception with LoRa 'reliable'.

### Sending and receiving reliable packets with cyclic redundancy checks

Ideally the process of sending and receiving a 'reliable' packet, including perhaps an optional acknowledged packet transmission, would be built into library functions with the transmitter functions able to send data such that the receiver has a way of knowing with a high level of certainty that the received data is valid and as intended. 

As was discussed in the post about data transfers;

[https://stuartsprojects.github.io/2021/02/26/Large-Data-Transfers-with-LoRa-Part1.html](https://stuartsprojects.github.io/2021/02/26/Large-Data-Transfers-with-LoRa-Part1.html)

You can, in the transmit function, create a cyclic redundancy check (CRC)  value for the data payload and include this 16 bit CRC as two bytes in the the outgoing packet. In the case of these reliable packets this CRC is appended to the end of the payload part of the packet. On receipt the receiver library function would read in the internal LoRa device packet data buffer, calculate the CRC of the data payload and compare this newly calculated CRC with the two byte CRC appended to the end of the packet at transmission. If the CRCs did not match then this could be assumed to be a rogue packet or just not for that receiver and rejected. The chance of a rogue packet being accepted as valid by checking payload CRCs in this way would then be around 1 in 65,535 packets.

The CRC checking should ideally operate in the background and although there may be library routines to extract the payload CRC, there in no compelling need for the Arduino sketch itself to do so.

### Sending and receiving reliable packets with NetworkID

Additional protection is added to the packet by the use of a 16 bit NetworkID. The ID number (0-65535) is defined for a particular application so the transmitter and receiver need to use the same NetworkID. The receiver can then reject packets that do not match its own NetworkID. The NetworkID can also be used to direct packets to particular receivers.

The packet structure used for reliable packets is;

<payload - 0 to 251 bytes> <NetworkID - 2 bytes> <payload CRC - 2 bytes> 


## Reliable packet demonstration examples

There are a number of examples using reliable packets in the \examples folder under \Reliable see here for the full library;

[https://github.com/StuartsProjects/SX12XX-LoRa](https://github.com/StuartsProjects/SX12XX-LoRa "SX12XX-LoRa-library")

The simplest examples are 201\_Reliable\_Transmitter and  201\_Reliable\_Receiver which transmit and receive a short payload 'Hello World'. 

### Examples '201\_Reliable\_Transmitter' and '202\_Reliable\_Receiver'.

The basic reliable transmit function used is;

LT.transmitReliable(buff, sizeof(buff), NetworkID, TXtimeout, TXpower, WAIT\_TX))

The buff defined in the transmit function is the name of a byte array that has been loaded with the payload, this can either be a sequence of characters, numbers or a structure. The receiver function also needs to know the size of this byte array. NetworkID is the ID for a particular application and needs to match the ID in the receiver. TXtimeout is the time in mS to wait for a transmission to finish, its used to stop the LoRa device potentially locking up a sketch\program. TXpower is the transmit power in dBm to be used and WAIT_TX tells the function to wait for the transmit to complete before exiting. 

The matching receive function is;

LT.receiveReliable(RXBUFFER, RXBUFFER\_SIZE, NetworkID, RXtimeout, WAIT\_RX);

The receiver fills the defined RXBUFFER with the contents of the received packet if its valid. The size of this buffer\array specified in RXBUFFER\_SIZE needs to be large enough to accommodate the largest payload size intended to be received. Due to the 4 bytes used by the packet for the NetworkID and payload CRC, the largest payload that can be received is 251 bytes.

The payload is copied on receive into the RXBUFFER array. If the transmitted payload array is only say 16 bytes long, and the transmit function increases the packet size by 4 to accommodate the NetworkID and payload CRC the packet length will be 20 bytes. Note that any packets arriving with a length greater than the size defined for RXBUFFER plus 4 bytes will be rejected.  

The NetworkID specified in the receive function has to match that used by the transmitter if the packet is to be accepted. 

The RXtimeout parameter is the number of milli seconds the receive function will wait for a packet to be received, if the period is exceeded a time-out will be reported. To not use the time-out function set RXtimeout to 0. 

WAIT\_RX makes the receive function wait for the receipt of a packet to complete before exiting.

The transmitter program prints to the Arduino IDE serial monitor the NetworkID and payloadCRC (the payload is "Hello World") and when the receiver picks up the packet this payload should also be printed out. If the NetworkID of the transmitter does not match the NetworkID of the receiver the receiver reports the error. If the payload CRC that the transmitter appends to the packet does not match the CRC that the receiver calculates on the payload, this is also reported as an error. 

Thus in the receive program '202\_Basic\_Reliable\_Receiver' the code in the 'packet\_is\_OK()' function is only executed it the received packet passes both the NetworkID and payload CRC checks. 

With these example programs if you change the NetworkID either in the transmit or receive program and you should see the receiver rejects them.

The transmitter program output looks like this;

	Transmit Payload > Hello World
	LocalNetworkID,0x3210,TransmittedPayloadCRC,0xBC69
If the receiver reports a valid packet which passes NetworkID and CRC checks the output looks like;

	Payload received OK > Hello World
	LocalNetworkID,0x3210,TransmitterNetworkID,0x3210,LocalPayloadCRC,0xBC69,RXPayloadCRC,0xBC69
If the networkID check fails the output can look like this;

	Error LocalNetworkID,0x3210,TransmitterNetworkID,0x55AB,LocalPayloadCRC,0xBC69,RXPayloadCRC,0xBC69

If the payload CRC check fails the output looks like;

	Error LocalNetworkID,0x3210,TransmitterNetworkID,0x1234,LocalPayloadCRC,0xBC69,RXPayloadCRC,0xBC69,ReliableIDError
If you dont want to use the CRC checking of the payload it can be turned off with this command;

	LT.setReliableConfig(NoReliableCRC);      //disable payload CRC check

You need to use the command on both the transmitter and the receiver. 


## Using reliable packets to control stuff

Sending Hello World messages is a useful start but a more practical example is to use the payload to control something on the receiver. The reliable examples show how the sent payload can be a variables in a structure, variables written direct to an array and how to write the variables for the payload direct into the LoRa devices internal buffer and thus bypassing the need for an memory array.  

### Using a structure

The next example 203\_Reliable\_Transmitter\_Controller\_Structure uses a structure as the payload;

	struct controllerStructure
	{
  	uint16\_t destinationNode;
  	uint8\_t outputNumber;
  	uint8\_t onoroff;
	};

The destinationNode is a 16 bit number that the receiver reads to see if the received packet is destined for that receiver. You can set outputNumber to control a range of outputs and onoroff is set to 1 to turn on the output and to 0 to turn it on. 

With the 203 transmitter example running load the 204\_Reliable\_Receiver\_Controller\_Structure program onto the receiver Arduino and it should report the packets received and the LED connected to the pin defined by LED1 should flash on and off. 

These structure examples would be easy to expand to control more outputs such as servos and similar. 

## Writing variables direct to an array

A SX12xx library file, arrayRW.h has routines that allow for variables to be directly written to an array. For example if the name of the array is 'controlarray' and then if you review the transmitter program, 205\_Reliable\_Transmitter\_Controller\_ArrayRW, you will see it fills the array with variables like this;

	beginarrayRW(controlarray, 0);                //start writing to array at location 0
	arrayWriteUint16(destinationNode);
	arrayWriteUint8(outputNumber);
	arrayWriteUint8(onoroff);                     //0 for off, 1 for on
	controlarraysize = endarrayRW();              //this function returns the length of the array to send 

When the 206\_Reliable\_Receive\_Controller\_ArrayRW program receives the packet its loaded into a defined array (RXBUFFER in the example) and the sketch then reads the variables from the the array like this;

	beginarrayRW(RXBUFFER, 0);                     //start reading from array at location 0
	destinationNode = arrayReadUint16();
	outputNumber = arrayReadUint8();
	onoroff = arrayReadUint8();                    //0 for off, 1 for on
	controlarraysize = endarrayRW();               //this function returns the length of the array to send


As with the previous examples the LED on the receiver should flash on and off.

The full set of write variable functions provided by the arrayRW.h library file is;

	arrayWriteUint8(uint8_t buffdata)
	arrayWriteInt8(int8_t buffdata)
	arrayWriteChar(char buffdata)
	arrayWriteUint16(uint16_t buffdata)
	arrayWriteInt16(int16_t buffdata)
	arrayWriteFloat(float tempf)
	arrayWriteUint32(uint32_t tempdata)
	arrayWriteInt32(int32_t tempdata)
	arrayWriteCharArray(char *buff, uint8_t len)
	arrayWriteByteArray(uint8_t *buff, uint8_t len)

And the read functions are;

	uint8_t arrayReadUint8()
	int8_t arrayReadInt8()
	char arrayReadChar()
	uint16_t arrayReadUint16()
	int16_t arrayReadInt16()
	float arrayReadFloat()
	uint32_t arrayReadUint32()
	int32_t arrayReadInt32()
	arrayReadCharArray(char *buff, uint8_t len)
	arrayReadByteArray(uint8_t *buff, uint8_t len)
 

## Low memory controller

Programs 207\_Reliabl\e_SXTransmitter\_Controller and 208\_Reliable\_SXReceiver\_Controller demonstrate how use the SX12XX library to directly write and read variables to and from the LoRa devices internal buffer directly for transmit and receive functions. There is no memory array required to be defined filled or used by transmitter and receiver programs. If large packets are being sent and received this can save a significant amount of memory.  

The 207 and 208 examples follow the same controller example of programs 203 and 204 in that an LED on the receiver should flash on and off.

The transmitter packet is loaded into the LoRa devices buffer like this;

	LT.startWriteSXBuffer(0);                          //start the write at SX12XX internal buffer location 0
	LT.writeUint16(destinationNode);                   //destination node for packet 
	LT.writeUint8(outputNumber);                       //output number on receiver
	LT.writeUint8(onoroff);                            //0 for off, 1 for on
	TXPayloadL = LT.endWriteSXBuffer();                //closes packet write and returns the length of the payload to send

And the receiver loads the variables from the LoRa devices buffer like this;

	LT.startReadSXBuffer(0);                       //start buffer read at location 0
	destinationNode = LT.readUint16();             //load the destination node 
	outputNumber = LT.readUint8();                 //load the output number
	onoroff = LT.readUint8();                      //0 for off, 1 for on
	RXPayloadL = LT.endReadSXBuffer();             //this function returns the length of the array read

Clearly with these direct accesses to the LoRa devices buffer the order and type of variables written has to match the order when read out by the receiver.

The full set of library routines for writing direct to the LoRa devices buffer is;

	writeUint8(uint8_t x);
    writeInt8(int8_t x);
    writeChar(char x);
    writeUint16(uint16_t x);
    writeInt16(int16_t x);
    writeUint32(uint32_t x);
    writeInt32(int32_t x);
    writeFloat(float x);
    writeBuffer(uint8_t *txbuffer, uint8_t size);   //uint8_t buffer
    writeBufferChar(char *txbuffer, uint8_t size);  //char buffer

And for reading direct from the LoRa devices buffer;

    uint8_t readUint8();
    int8_t readInt8();
    char readChar();
    uint16_t readUint16();
    int16_t readInt16();
    uint32_t readUint32();
    int32_t readInt32();
    float readFloat();
    uint8_t readBuffer(uint8_t *rxbuffer);          //reads buffer till a null 0x00 is reached
    uint8_t readBuffer(uint8_t *rxbuffer, uint8_t size);
    uint8_t readBufferChar(char *rxbuffer);


#### Getting the payload CRC in a sketch

Note that the CRC of the actual payload, that is appended to the packet and printed out in the examples, can be obtained by the sketch in several ways, first the simplest way is to use a SX12XX library function after the packet has been transmitted or received;

	TXpayloadCRC = getTXPayloadCRC();
	RXpayloadCRC = getRXPayloadCRC();

The payload CRC can be read direct from the end of the packet buffer of the LoRa device like this;

	PayloadCRC = LT.readUint16SXBuffer(PayloadL+2);

Where Payloadl is the length in bytes of the payload, this is the method used in the 201 transmitter example. 

Or the payload CRC can be calculated locally by accessing the local payload array\buffer directly like this which uses another SX12xx library function;

	LocalPayloadCRC = LT.CRCCCITT(buff, sizeof(buff), 0xFFFF); 

Where buff is the name of the array containing the payload.
Where 0xFFFF is the CRC check start value, normally 0xFFFF.

And finally there is a SX12xx library function that will return the CRC of an area of the LoRa devices buffer like this;

	CRCCCITTReliable(0, PayloadL-1, 0xFFFF);

Where PayloadL is the length of the payload array\buffer. 

Where 0 is the address in the buffer to start the CRC check.
Where 0xFFFF is the CRC check start value, normally 0xFFFF. 


#### Getting the NetworkID into a sketch

The NetworkID used for the transmission or reception of a packet, that is appended to the packet and printed out in the examples, can be obtained by the sketch using an SX12XX library function after the packet has been transmitted or received;

	TXNetworkID = LT.getTXNetworkID();
	RXNetworkID = LT.getRXNetworkID();


#### Next: using packet acknowledgements  

<br>
<br>

## Stuart Robinson
## August 2021