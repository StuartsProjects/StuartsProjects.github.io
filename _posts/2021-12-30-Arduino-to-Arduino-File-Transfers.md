---
layout: post
title: "Arduino to Arduino File Transfers"
date: "2021-12-28"
---

In a previous post;

[**https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html**](https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html)

I described a possible method of having the files or images received from a ESP32CAM or other Arduino transferred to a PC via a Serial port connection. The idea behind this was to have a simple method of viewing images as they are being received from a remote camera, when they arrive they pop up automagically in a folder on the Windows PC. Set the folder properties to images and you get a preview of the images as they arrive. Useful and simple for monitoring a remote security or wildlife camera or high altitude balloon flight.  

The method described in the above post does work and the set-up of Tera Term to receive the file transfers is quite simple. An alternative approach could be to use a custom application on the PC. The YModem protocol does deal with data errors as the file transfer progresses but some tests carried out simply sending each byte of a file from an Arduino over a serial port to a PC and have the PC capture each byte into a file resulted in the serial transfers running error free. 

In one test I transferred a 64K image file from an Arduino as one long series of bytes with no acknowledge and verify processes operating. At 115200 baud I sent the image around 1,500 times with no errors. Thus it seemed that only limited error checking for a transfer would be needed, perhaps only a CRC on the completed file. 

So maybe an application for a PC without using the YModem protocol would be viable; but what language to use ? Python might be an obvious choice, but my Python coding skills are very limited. 

It was suggested to me that Processing might be an alternative, the language is similar in style to the C++ used on Arduino, so it might not be so difficult to convert a program known to be working on Arduino across to Processing. 

### Arduino to Arduino File Transfer

So as a pre-empt to (possibly) writing an application for a PC to receive files from an Arduino via a serial link, I decided to first try out the idea as an Arduino to Arduino serial file transfer. 

The idea was to send the transfer as a series of data blocks. These blocks would have a pair of bytes identifying the start and end of the block and at the end of the data payload part of the block there would be a CRC calculated on the data payload part of that block. This would be the basic structure of a block;

`<StartID><BlockType><Flags><BlockLength><DataPayload><DataPayloadCRC><EndID>`  

I initially thought to send a first block with the file name in it, have the receiver process that block and then send the file data itself as a second block. However the file name block is fairly short, maybe 32 bytes, whilst the file data block could be quite large so it seemed simpler to send both blocks joined together and have the receiver decode both in one process. The planned structure of the data sent for the file transfer is;

    <IDStart>         - 2 bytes
    <BlockType>       - 1 byte
    <Flags>           - 1 byte - not currently used
    <FilenameLength>  - 1 byte
    <FileName>        - n bytes
    <Filesize>        - 4 bytes
    <Filedata>        - n bytes
    <FiledataCRC>     - 2 bytes
    <IDEnd>           - 2 bytes 


The receiver program first empties the serial input buffer and then waits for serial characters to arrive. Each received serial character is then copied into a memory array. The code used for this copy is below;

	startuS = micros();	

   	while (((uint32_t) (micros() - startuS) < SerialTimeoutuS))`
    {
      if (SerialInput.available() > 0)
      {
        if (receivedBytes >= MaxMessageSize)
        {
         break;
        }
       Message[receivedBytes] = SerialInput.read();
       startuS = micros();        //restart timeout
       receivedBytes++;
      }
    };   
    
Note that there is a time-out, a period whereby if no characters arrive the routine assumes the file transfer has ended. The routine is the same as used in the Serial Bridge program ([**SX128X SerialBridge examples**](https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/SX128x_examples/SerialBridge) - 105\_LoRa\_Serial\_Bridge\_Transmitter) and the time out in that program was calculated based on the baud rate and a number of characters before assuming a time-out. For this file transfer program its enough to set the time-out at 0.5 second or 1 second. A pause of this length of time in the actual serial output from the transmitter is unlikely.

The receiving Arduino used needs enough memory to receive the combined file name header block and the file data block. Large memory Arduinos such as a DUE are suitable as would an ESP32. At this early point in the program development, I just wanted a working transmitter and receiver, at a later date it might be possible to enhance the program so that it could send the (possibly large) file data payload as a series of much smaller blocks so that the transfer programs would work on smaller memory Arduinos, but maybe that's for later.

With the transmitter Arduino (DUE in this case) loaded with program  **243\_Serial\_File\_Transfer\_TX.ino** and a copy of the $50SATL.jpg file on its SD card and the receiver loaded with program **244\_Serial\_File\_Transfer\_RX.ino** the respective serial monitor outputs look like this as the transfer progresses;

	\\Transmitter
    Y:\Data\Projects\SerialTransfer\243_Serial_File_Transfer_TX\243_Serial_File_Transfer_TX.ino
    Initializing SD card...SD Card initialized.
    Serial file transfer ready
    
    Serial Transfer started /$50SATL.JPG
    Filesize 0xF673
    Filename length 0xD
    SourcefileCRC 0x597E
    Writing header
    Writing file data
    Filebytes sent 0xF673
    Transmit time 5.399 seconds
    Program halted
    \\Receiver
    Y:\Data\Projects\SerialTransfer\244_Serial_File_Transfer_RX\244_Serial_File_Transfer_RX.ino
    Initializing SD card...SD Card initialized.
    Serial file transfer ready
    
    Clear serial buffer
    Waiting start of serial input
    Started 
    Serial timeout
    Serial bytes read 0xF68D
    Block start bytes > 11 22 81 01 0D 2F 24 35 30 53 41 54 4C 2E 4A 50 47 00 73 F6 00 00 FF D8 FF E0 00 10 4A 46 49 46 
    Block end bytes   > C2 09 C2 10 89 42 49 48 94 21 0A 16 00 48 9D D0 84 D4 92 ED 82 87 6E 84 2A A7 FF D9 7E 59 33 44 
    File data block start detected
    File data block end detected
    FilenameLength 0xD
    FileLength 0xF673
    FileStartAddress 0x16
    Open /$50SATL.JPG for writing
    File bytes written 0xF673
    CRC of file written to SD 0x597E
    File received OK
  

You can see that the transmitter reports the file length and CRC of the file contents and the receiver saves the file to its own SD card and prints out that saved files length and CRC, so that you can check if they match. 

Note the receiver prints this; Block start bytes > 11 22 which shows the defined IDStart, 0x2211. You can also see the start of the JPG file, FF D8. There is also this line; 
Block end bytes   > shows the 33 44 at the end which is the defined IDEnd. The FF D9 is the end of the JPG file, followed by its checksum 0x597E.

Also note that the file length and CRC are also sent out by the transmitter in the block so the receiver does know what it should be checking.  

Spooling the received data directly to an SD file (rather than a memory array) was tried, but with the Arduino serial buffer only at 64 bytes as standard, there is not a lot of room for time pauses or glitches in writing to the SD card, especially at high baud rates.  

The File transfer TX and RX programs can be found here;

[**https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/Hardware_Checks**](https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/Hardware_Checks)


**Stuart Robinson**

**December 2021**

