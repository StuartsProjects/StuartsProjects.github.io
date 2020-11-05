---
layout: post
title: "Low memory easy LoRa"
---

Most all LoRa® libraries for Arduino use a memory buffer to assemble the data that is to be transmitted as a packet. A packet can be up to 255 bytes long so you may need to allocate a memory buffer of 255 bytes to send and receive LoRa® packets. 

The data being sent will often be a mix variable types and you can do this by sending a structure (of different variable types) as the buffer, or just send the variables\numbers as ASCII characters and translate back into variables\numbers on packet receipt. Whilst these methods are standard Arduino code a lot of beginners do seem to have trouble understanding how these methods of coding work. 

The LoRa device has its own internal buffer where the packet data to be sent has to be loaded. The normal process involves assembling the packet or data to be sent in a memory buffer and then using the library code to send the buffer. The library code takes the buffer pointer and fills the LoRa devices internal buffer and then sends the packet. 

You can simplify this process (and save memory) by having library routines that fill the LoRa devices internal buffer directly. Here is an example, its a simulation of filling the LoRa device buffer with GPS tracker data and transmitting the packet;

	LoRa.startWriteSXBuffer(0);
	LoRa.writeBufferChar(trackerID, sizeof(trackerID));
	LoRa.writeFloat(latitude);
	LoRa.writeFloat(longitude);
	LoRa.writeUint16(altitude);
	LoRa.writeUint8(satellites);
	LoRa.writeUint16(voltage);
	LoRa.writeInt8(temperature);
	TXPacketL = LoRa.endWriteSXBuffer();
	BytesSent = LoRa.transmitSXBuffer(0, TXPacketL, 5000, TXpower, WAIT_TX);

Note that with the code above the LoRa devices buffer is filled with the bytes used to store the variable. There are LoRa libraries that allow the use of functions like LoRa.print(variable) to build a packet and the variable is sent in its ASCII form. So a float such as 51.23456 would be sent as 8 ASCII bytes. The LoRa.writeFloat(); function used above uses 4 bytes to send the float.  

At the receiver you set-up the receive function and when a packet arrives match the sequence used by the transmitter to extract data from the LoRa devices receive buffer, like this;

	RXPacketL = LoRa.receiveSXBuffer(0, 0, WAIT_RX);
	
	LoRa.startReadSXBuffer(0);
	LoRa.readBufferChar(receivebuffer);
	latitude = LoRa.readFloat();
	longitude = LoRa.readFloat();
	altitude = LoRa.readUint16();
	satellites = LoRa.readUint8();
	voltage = LoRa.readUint16();
	temperature = LoRa.readInt8();
	RXPacketL = LoRa.endReadSXBuffer(); 

The previously defined variables, receivebuffer, latitude, longitude, altitude, satellites, voltage and temperature are now filled with the values that were originally transmitted. No translation of ASCII characters into variables is needed.

For sure you need to keep track of the type of and sequence of variables being sent, but that's similar to copying a structure definition across from transmitter to receiver. The approach above is also easy to modify. 

One benefit of the above method of transmitting and receiving LoRa is that no memory needs to be allocated in the Arduino for the 'buffer' that is to be sent or received. 

The transmitter program above uses, on an Arduino Pro Mini, circa 15% of program storage space and 10% of dynamic memory. The receiver uses a similar amount.   

The full code for the programs discussed above are '8_LoRa_LowMemory_TX' and '9_LoRa_LowMemory_RX' which will be found in the folder \examples\SX127x_examples\LowMemory of the library to be found here;

[SX12xx-LoRa Library](https://github.com/StuartsProjects/SX12XX-LoRa)

Stuart Robinson
November 2020