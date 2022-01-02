---
layout: post
title: "StuartCAM - ESP32CAM Getting the Pictures Onto a PC"
date: "2022-01-01"
---


Having the ESP32CAM transmit its pictures via LoRa to an Arduino which then saves the pictures onto an SD card is sort of OK, but its a bit tiresome to keep taking the SD card out of the Arduino and plugging it into a PC to view the images. 

You could use a ESP32 as a web sever connected to a local WiFi and display the images on a tablet, phone or PC. This in principle is how this set-up tutorial for the ESP32CAM works;

[**https://randomnerdtutorials.com/esp32-cam-video-streaming-face-recognition-arduino-ide/**](https://randomnerdtutorials.com/esp32-cam-video-streaming-face-recognition-arduino-ide/)

But what if you don't have a local WiFi or for resilience purposes you want to have all the received pictures transferred directly to a folder on the PC ?

With the very much appreciated help of Arduino forum member @markd833 who  produced some YModem transfer code with the core based on an implementation of YModem by Daniel Mack from;

[**https://gist.github.com/zonque/0ae2dc8cedbcdbd9b933**](https://gist.github.com/zonque/0ae2dc8cedbcdbd9b933)

It was possible to transfer a file or image from the SD card on an Arduino to a specified folder on a PC using the YModem receive capability of the PC based Tera Term Serial terminal program. The details of the set-up required are outlined here;

[**https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html**](https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html) 

What's now needed is to integrate the transfer to PC functions into the LoRa receiver program for the ESP32CAM, i.e. program **239\_StuartCAM\_LoRa\_Receiver.ino** 

The hardware Serial port, with the name Serial on most Arduinos is also the program download port, is used for the YModem transfer port to the PC. The set-up of Tera Term to accept the YModem transfers was described in an earlier post;

[**https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html**](https://stuartsprojects.github.io/2021/12/28/Arduino-to-PC-File-Transfers.html)

To monitor the progress of the receiver as it picks up the images (via LoRa) from the remote camera and they are then transferred to the PC, you need to attach a USB to Serial adapter to the receiver, either to another of the Arduinos hardware serial ports (the DUE has 4 hardware serial ports) or via a software serial instance. 

Here is a ATmega1284P board I built acting as the receiver of images from an ESP32CAM, the YModem serial download to PC is the connection in the middle (also the programming port) and the monitoring port is at the bottom. This ATmega1284P board has an SD card on the back, or a module can be connected to one of the rows of pin connectors. 

<br>
<p float="left">
  <img width="250" img src="/images/LoRa_Receiver_Ymodem1.jpg" />
  <img width="140" img src="/images/LoRa_Receiver_Ymodem2.jpg" /> 
</p>
<br>

Note that for monitoring the receiver code also drives an ILI9341 TFT display, but you don't have to use it. Here is the receiver running on a DUE with the display connected;

<br>
<p align="center">
  <img width="450"  src="/images/LoRa_Receiver_Ymodem5.jpg">
</p>
<br> 


To test the initial set-up I used program **233\_SDfile\_Transfer\_Transmitter.ino** and modified it to send the same image repeatedly with a 15 second gap between transmissions. These were the Tera Term screens, for the YModem transfer at the top, and another Tera Term instance watching the monitor output on the bottom;


<br>
<p align="center">
  <img width="450"  src="/images/LoRa_Receiver_Ymodem3.jpg">
</p>
<br> 

And here is a screenshot of the folder where the images are arriving; 


<br>
<p align="center">
  <img width="450"  src="/images/LoRa_Receiver_Ymodem4.jpg">
</p>
<br> 

Note that Tera Term recognises that a duplicate image is being transferred and gives each a unique number. 


### Timeouts on Tera Term

I noticed that if you leave the transfers and YModem uploads running, that after the first image is sent when the next YModem transfer starts there is a delay after the PC picks up the file name of the impending transfer, before the actual transfer starts. This delay was around 20 seconds. If this delay is a problem then when you load the TERATERM.INI file, located in the Tera Term program folder into an editor, you will find this section;
  
    ; YMODEM Timeout value(v1,v2,v3,v4,v5) by seconds
    ;  v1=NAK mode: Timeout value for first packet
    ;  v2=CRC mode: Timeout value for first packet
    ;  v3=Timeout short time 
    ;  v4=Timeout long time 
    ;  v5=Timeout very long time 
    YmodemTimeouts=10,3,10,20,60

Note the line; YmodemTimeouts=10,3,10,**20**,60. The value 20, highlighted in bold can be changed to say 1 and the delay mentioned above does not occur. 


### Wildlife CAM 

With the ESP32CAM watching the hedgehog hole in the fence in my garden I set it up to take regular pictures and left it running for a while. The two Tera Term sessions are shown below;

<br>
<p align="center">
  <img width="650"  src="/images/WildlifeCAM1.jpg">
</p>
<br> 

And after an hour or so the pictures folder;

<br>
<p align="center">
  <img width="450"  src="/images/WildlifeCAM2.jpg">
</p>
<br>

Whats useful about the WildlifeCAM setup is that once setup you can just leave it running. 

The receiver program is 245\_StuartCAM\_LoRa\_Receiver\_PCTransfer.ino running on an Arduino DUE which you can find here;

[**https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/SX127x_examples/Camera**](https://github.com/StuartsProjects/SX12XX-LoRa/tree/master/examples/SX127x_examples/Camera)

At the current version the receiver needs an SD card, but it should be possible to eliminate the need for an SD card if you use a Microcontroller with enough memory to download the entire image to a memory array before passing across to the YModem transfer.  



### Stuart Robinson

### January 2022

