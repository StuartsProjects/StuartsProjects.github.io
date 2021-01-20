---
layout: post
title: "The Perils of Using Structures ?"
---

Often when there is a discussion on how to send a mixture of variables and maybe types too as a data packet over a radio link with Arduino, I have noticed that using a structure is often suggested as the 'obvious' solution. 

In general I avoid using structures for sending packets of data and for good reasons. If the transmitter and receiver platform are the same, then everything works just fine. However, if the sending and receiving Arduinos use different platforms, say an Atmega328P (Pro Mini or UNO) for the transmitter and ESP32 or DUE for the receiver, the receiver will not be able to decode the packet and displays garbage. The packet containing the structure can also be as much as 33% longer than it needs to be. 

The problem with structures was highlighted to me when I was working on a secure and reliable method of transmitting a packet and the transmitter then knowing, with a very high level of certainty, that the receiver had actually received the packet set. In other words a secure send and acknowledge system. The transmitted packet needed to have a header (in addition to the data buffer sent) and this header would contain check bytes, such as maybe a 'networkID' that the receiver could use to verify the source of the data. It would have been possible to hard code the header format into the library functions, but this would not be very flexible. So I decided that the transmitted packet would have have a array/buffer as a header and another as the data buffer. 

Now it does initially seem like a good idea for the header to be a structure, but that does not work if the transmitter and receiver are on non-compatible Arduino platforms. 

Take this structure for example, typical maybe for the data sent by a LoRa based GPS tracker;

    struct trackerPacket
    {
      uint8_t trackerID[9];
      uint32_t txcount;
      float latitude;
      float longitude;
      uint16_t altitude;
      uint8_t satellites;
      uint16_t voltage;
      int8_t temperature;
    };

After defining the structure you read the GPS and set the values;

    //fill the defined structure with values
    uint8_t buff[] = "tracker1";
    memcpy (&location1.trackerID, &buff, sizeof(buff));
    location1.txcount = TXpacketCount;
    location1.latitude = 51.23456;
    location1.longitude = -3.12345;
    location1.altitude = 199;
    location1.satellites = 8;
    location1.voltage = 3999;
    location1.temperature = -9;

When transmitted, as a debug aid, the transmitter program outputs this to the serial monitor;

   
    1 27 Bytes Sent 67mS
    74 72 61 63 6B 65 72 31 00 01 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08 9F 0F F7

So the structure is 27 bytes long and the contents in HEX are shown.

At the receiver end we have the same structure definition and this is the serial monitor printout;

	1  tracker1,88,51.23456,-3.12345,199m,8sats,3999mV,-9c	74 72 61 63 6B 65 72 31 00 58 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08 9F 0F F7

So, trackerID, latitude, longitude etc all received and decoded correctly, all is hunky-dorey. 

So now lets try the exact same receiver program on an Arduino DUE, this is the receiver output;

    1  tracker1,1290809344,118070.51562,0.00000,3999m,247sats,0mV,0c
    74 72 61 63 6B 65 72 31 00 0B 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08 9F 0F F7

So the exact same data (the packet) is received but it displays as garbage, apart from the tracker ID. 

So lets check what the DUE would have sent if it was the transmitter;

    1 32 Bytes Sent 72mS
    74 72 61 63 6B 65 72 31 00 00 00 00 01 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08 00 9F 0F F7 00

Whats this ??? The DUE sends 32 bytes whilst the Pro Mini sends only 27 ???

Lets line up the two HEX printouts, Pro Mini first;

    Pro Mini  74 72 61 63 6B 65 72 31 00          01 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08    9F 0F F7
    DUE       74 72 61 63 6B 65 72 31 00 00 00 00 01 00 00 00 30 F0 4C 42 9B E6 47 C0 C7 00 08 00 9F 0F F7 00 
 
The DUE structure has padding bytes, three 0x00s between the character buffer and the uint32_t (TXpacketCount) and then another 0x00 between a uint8_t (satellites) and a uint16_t (voltage) and finally an extra 0x00 on the end of the packet. 

So the different platforms, ATmeaga328 for the Pro Mini and SAM3X8E for the DUE use different structure formats. Also note that even the Pro Mini adds padding bytes, with the character buffer 9 bytes long the rest of the data (uint32_t, float, float, uint16_t, uint8_t, uint16_t, int8_t) makes the data only 24 bytes. So the DUE in this case is sending a packet that is 32 bytes long when only 24 are needed to actually send the data.  

Thus whilst a structure might be the 'obvious' solution to mixing data types in transmitted packets, you need to be sure the Arduino platforms you are using for transmitter and receiver are compatible. This is why I avoid using structures in my libraries example programs. Also be aware that the data packet might be 33% longer than it needs to be. 

To ensure the **[SX12xx LoRa Library](https://github.com/StuartsProjects/SX12XX-LoRa)** programs are compatible across platforms, I added functions to the library that allow an array (buffer) for transmit to be built in this way;

    LoRa.startWriteSXBuffer(0);
    LoRa.writeBufferChar(trackerID, sizeof(trackerID));
    LoRa.writeUint8(txcount); 
    LoRa.writeFloat(latitude);
    LoRa.writeFloat(longitude);
    LoRa.writeUint16(altitude);
    LoRa.writeUint8(satellites);
    LoRa.writeUint16(voltage);
    LoRa.writeInt8(temperature);
    LoRa.endWriteSXBuffer();

And to decode the packet at the receiver you do this;

    LoRa.startReadSXBuffer(0);
    LoRa.readBufferChar(receivebuffer);
    txcount = LoRa.readUint8();
    latitude = LoRa.readFloat();
    longitude = LoRa.readFloat();
    altitude = LoRa.readUint16();
    satellites = LoRa.readUint8();
    voltage = LoRa.readUint16();
    temperature = LoRa.readInt8();
    LoRa.endReadSXBuffer();

In this case the length of the packet is 24 bytes,  versus 27 for the Pro Mini and 32 for the DUE when they use structures. 

Have fun. 


#### **January 2021**     