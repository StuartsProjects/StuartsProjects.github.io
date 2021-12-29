---
layout: post
title: "Arduino to PC File Transfers"
date: "2021-12-28"
---

With the software to transmit files or images over LoRa working on Arduino some method of easily displaying the received images on a PC screen would be useful. 

My simple idea is to transfer the files or images via a serial port from the Arduino receiving the files or images (via LoRa) to a PC and have the files or images appear automagically in a folder for viewing. In this way keeping an eye on images coming from a remote security camera or high altitude balloon would be easy. 

I thought this transfer might be done with a YModem transfer protocol running on the Arduino and have the PC receive then receive the transfers via Tera Term, which has YModem capability built in. I asked about YModem transfers over in the Arduino forums and member @markd833 produced some code with the core based on an implementation of YModem by Daniel Mack from;

[**https://gist.github.com/zonque/0ae2dc8cedbcdbd9b933**](https://gist.github.com/zonque/0ae2dc8cedbcdbd9b933)

Following the principle that an application is often best built in stages, I first used the code to do a YModem transfer of a single file on an Arduinos SD card across to the PC, this is the program **238\_Arduino\_to\_PC\_File\_Transfer\_YModem.ino** which is found here in the SX12XX-LoRa library;

**[https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/SX127x_examples/DataTransfer](https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/SX127x_examples/DataTransfer)**

The Arduino sending the file\image via YModem was a DUE in this case, it had the file $50SATL.JPG on its SD card and the program repeatedly attempts to transfer the file via the DUEs Serial2 port which is in turn connected to a USB to serial adapter plugged into a PC. With the program running the Arduino Serial monitor should show this;

    Using Serial2 for YModem comms @ 115200 baud.
    Initializing SD card...
    SD Card initialized.
    Start transfer 1  $50SATL.JPG
    Waiting for receiver ping ...


### Tera Term - Setup Serial Port  

On my PC, Windows7, the USB to serial adapter turns up on port COM42 so in Tera Term select \Setup\Serial Port... and configure for 115200 baud, see below;

<p align="center">
  <img width="400"  src="/images/YModem1.jpg">
</p>
<br>

<br>

I used version 4.106 of Tera Term.

### Tera Term - Setup File Transfer directory

Tera Term needs to know where to save the files transferred, we need to change it, it will default to its own program directory. I created a folder on my desktop called YModem and in Tera Term selected \File\Change Directory and browsed to the YModem directory;


<p align="center">
  <img width="400"  src="/images/YModem2.jpg">
</p>
<br>

<br>

### Tera Term - Start File Transfers

To start Tera Term file transfers running, in Tera Term select, \File\Transfer\YMODEM\Receive..


<p align="center">
  <img width="400"  src="/images/YModem3.jpg">
</p>
<br>


And thats it, the transmitting program, **238\_Arduino\_to\_PC\_File\_Transfer_YModem.ino**, should attempt to transfer the $50SATL.JPG file with a 15 second delay between transfers. Note that the Tera Term receiver will recognise that the same file is being transferred and the destination files will be named, $50SATL.JPG, $50SATL1.JPG, $50SATL2.JPG etc. 

The Serial monitor output of the Arduino doing the YModem transfer should display this for every completed transfer;

    Sending $50SATL.JPG
    63091 bytes
    24 35 30 53 41 54 4C 2E 4A 50 47 00 36 33 30 39 31 00 00 00
    done.
    Transfer finished


**Stuart Robinson**

**December 2021**

