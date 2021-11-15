---
layout: post
title: "StuartCAM - LoRa Remote Camera Image Transfer"
date: "2021-11-12"
---


## Remote camera image transfer

One of the potential applications for the recently published data transfer functions for the [**SX12XX-LoRa**](https://github.com/StuartsProjects/SX12XX-LoRa) library is to receive pictures from a remote Arduino camera. And since this is using LoRa these images could be transmitted over long distances. There are many different image types and resolutions, but as long as the image can be saved as a file on an SD card or transferred directly from an array, the image can be transferred reliably using LoRa and the image be saved as a file on the SD card of the remote receiver for later viewing. 

I chose an Arducam OV2640 ([**ArduCAM Mini 2MP OV2640**](https://www.arducam.com/product/arducam-2mp-spi-camera-b0067-arduino/)) as the camera together with the Arducam library ([**Arducam Library)**](https://github.com/ArduCAM/Arduino). This software was easy to use and does the job very well. I like this camera, it uses hardware SPI and I2C for the interface, so if the host Arduino is already using these interfaces then only one extra IO pin is needed, the SPI CS pin for the camera. 

The Arducam library provides the routines for setting up and using the camera and there is a provided example, **ArduCAM\_Mini\_Capture2SD** that takes pictures and saves them to an SD card. 

The working image transfer program is **238\_StuartCAM\_LoRa\_Remote\_Camera**. The program starts up and first tests the SD card can be accessed and then checks the SPI and I2C interfaces used to communicate with the OV2640. The results of these tests are shown on the serial monitor. The program then takes a picture with the camera and stores it on SD. The file name is passed across to be transferred via LoRa to a remote receiving Arduino which is using program **234\_SDfile\_Transfer\_Receiver** or **239\_StuartCAM\_LoRa\_Receiver**. When the transfer is completed the receiving Arduinos SD card should hold a copy of the same image name originally taken by the OV2640. 

For most of the data transfer testing I have been using a shield PCB for the Arduino DUE that has two Mikrobus compatible sockets for taking plug in LoRa modules, this makes it easy to swap between the various LoRa devices. The shield PCB has a socket for a micro SD card. See picture below, a 434Mhz LoRa module is plugged in to the shield, on the extreme left is a 2.4Ghz plug in LoRa module.

<br>
<p align="center">
  <img width="450"  src="/images/DUE_Shield_Mikrobus.jpg">
</p>
<br>

I put the OV2640 connected to the shield and DUE into a small box together with power bank so I could take it up a nearby mountain top to see what the pictures would be like;

<br>
<p align="center">
  <img width="450"  src="/images/StuartCAM_in_box.jpg">
</p>
<br>
 
To receive the pictures I used the Arduino DUE shield with a TFT display plugged in so I could see the transfer in progress, the program used was **239\_StuartCAM\_LoRa\_Receiver**. This program will not fit on an Atmega328P powered Arduino, but is fine on a DUE. The basic file transfer receiver program, **234\_SDfile\_Transfer\_Receiver**, can also be used as the receiver and will fit on a ATmega328P, but there is no display. 
 
<br>
<p align="center">
  <img width="450"  src="/images/StuartCAM_Receiver.jpg">
</p>
<br>

When I got back home I took the microSD card out of the shield and looked at the images on a PC, this is typical;

<br>
<p align="center">
  <img width="450"  src="/images/StuartCAM3.jpg">
</p>
<br>


OK, so in this test the images were only being transferred a few feet, but the LoRa settings used should provide a line of sight range approaching some 10s of kilometres. On this hilltop the 640 x 480 pictures were around 24,000 bytes and took around 14 seconds to transfer and be saved to SD card. 

There is no particular need to use an Arduino DUE with a large power bank as the transmitter, the program transmitter program will fit in an Atmega328P Arduino. Below is the DIY ATmega1284P board I was using. The ATmega1284P has a lot more memory than the ATmega328P. The board takes a single Mikrobus compatible plug in module and is all 3.3V of course. The board can be built without needing any surface mount components. 

<br>
<p align="center">
  <img width="450"  src="/images/StuartCAM_ATmega1284P.jpg">
</p>
<br>

### Transfer time - 434Mhz LoRa vs 2.4Ghz LoRa vs 2.4Ghz FLRC

So maybe if you wanted to transfer images over long distances and you don't mind waiting a bit, then UHF LoRa (434Mhz, 868Mhz or 915Mhz) could be the way to go. Unfortunately bear in mind that legal duty cycle restrictions will be a significant issue.  

I wanted to do a comparison of the transfer times for a possible application, garden watch. There is a hedgehog hole in the fence of my garden to allow them spiky things to roam around. I am sure there might be local rodents using it, so I aimed the StuartCAM at the hole in the fence, set up the transfer for 434Mhz LoRa and tested the transfer time, a typical image was around 46,000 bytes;

<br>
<p align="center">
  <img width="450"  src="/images/GardenWatch.jpg">
</p>
<br>  

The transfer time with the SX1278 was 26.3 seconds. The SX127X devices can be run a bit faster at spreading factor 6, which would reduce the air time of the packets by about 43%, but for the SX127X devices that means sticking to fixed size packets, which does complicate things quite a bit. A far better choice for use in the UHF bands would be the SX126X devices, which not only allow for variable packet sizes at all spreading factors, but can also use spreading factor 5. SF5 would reduce the packet air time versus SX127X transfer detailed above by 66% so around 3 times faster.

The SX126X series can also be used at the low bandwidth of 20.8Khz, which in the 434Mhz band in some places, including the UK, allow for 100% duty cycle use. A calculation suggests that the 46,000 byte image transfer would then take around 8 times longer.   

I next tried the various modes possible with the 2.4Ghz SX1280 device as a comparison, the results are below;

<table>
<thead>
<tr>
  <th>Packet Type</th>
  <th>Frequency</th>
  <th>Modem Settings</th>
  <th>File Size</th>
  <th>Transfer Time</th>
</tr>
</thead>
<tbody>

<tr>
  <td>LoRa</td>
  <td>434Mhz</td>
  <td>SF7,BW500K,CR4:5</td>
  <td>46,000</td>
  <td>26.3 seconds</td>
</tr>

<tr>
  <td>LoRa</td>
  <td>2.4Ghz</td>
  <td>SF5,BW1600K,CR4:5</td>
  <td>46,000</td>
  <td>3.4 seconds</td>
</tr>

<tr>
  <td>FLRC</td>
  <td>2.4Ghz</td>
  <td>BR260,BW1_2,CR_0_3</td>
  <td>46,000</td>
  <td>3.9 seconds</td>
</tr>

<tr>
  <td>FLRC</td>
  <td>2.4Ghz</td>
  <td>BR1300,BW1_2,CR_1_0</td>
  <td>46,000</td>
  <td>1.9 seconds</td>
</tr>

</tbody>
</table>

The  **239\_StuartCAM\_LoRa\_Remote\_Camera** and **239\_StuartCAM\_LoRa\_Receiver** programs are currently in versions for the UHF SX127X LoRa devices and the 2.4Ghz SX128X LoRa devices in the respective \Camera example folders. Hope to add SX126X device support soon.

One point to note is that the 260kbps FLRC setting (BR260,BW1_2,CR_0_3) results in a slower transfer than the 203kbps LoRa (SF5,BW1600K,CR4:5) set up. This is not perverse, LoRa allows for 255 byte packets whilst FLRC is limited to 127 bytes, so the number of packets needing to be sent with FLRC is doubled. You might think there is therefore no point in using FLRC at this 260kbps setting, but its worth a try since the data sheet quotes it as having an extra 4dBm of receive sensitivity so the range\distance covered ought to be about 60% greater. 

### Can we run StuartCAM on an ESP32CAM ?

The answer is yes. There are versions of the SX127X data transfer functions that will work without the DIO0 pin to sense transmit and receive done and these are needed because the ESP32CAM is very pin restricted. 

The file transfer software was changed a bit so that the SD card was not needed, instead the array that the ESP32 camera stores the picture in is transferred directly. The difficulty with the ESP32 camera is getting the exposure correct, it seems to be all over the place, images are either too bright and washed out or very dark, one day maybe I will work out why. 


<br>


**Stuart Robinson**

**November 2021**