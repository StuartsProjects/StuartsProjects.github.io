---
layout: post
title: "StuartCAM - ESP32CAM Picture Transfers with LoRa"
date: "2021-11-21"
---

The ESP32CAM is a camera module which you can use to send pictures over long distances with SX127X LoRa devices. With the high speed 2.4Ghz SX128X LoRa devices, you can transfer a 800x600 image in around 1 second.  

With an ESP32CAM you get an ESP32, a camera, power regulator and an SD card holder all in a fairly small and light 8g PCB;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_1.jpg">
</p>
<br>


This 'new' post is a combination of two previous posts where the trials and tribulations of getting a LoRa device to work reliably on an ESP32CAM were described. The second of the posts described getting the SD card working as well and to reduce confusion I have put all the content in one post so that the schematics and connections described at the end are those used by the sketches in the SX128X library, so lets continue;


I previously had a LoRa device working with an ESP32CAM, the adventures are described here;

[**Easy ESP32CAM LoRa Tracker Add On Board**](https://stuartsprojects.github.io/2020/12/17/Easy-ESP32CAM-LoRa-Tracker-Add-On-Board.html)

Those adventures were back in December 2020 and at that time one future application I thought of was to be able to transfer pictures taken by the camera via LoRa to a remote receiver and potentially do this over long distances. 

In September\October 2021 I wrote some working data transfer software for the Semtech LoRa SX127X devices. This software could reliably transfer a file from the SD card of one Arduino to the SD card of another using LoRa. The code for UHF SX127X and 2.4Ghz SX128X devices was added to the [**SX12XX-LoRa**](https://github.com/StuartsProjects/SX12XX-LoRa) library in early November 2021. There are example sketches that would transfer an image taken by an OV2640 camera using the Arducam library and save it on the SD card of a receiving Arduino, see here;

[**StuartCAM - LoRa Remote Camera Image Transfer**](https://stuartsprojects.github.io/2021/11/12/StuartCAM-LoRa-Remote-Camera-Image-Transfer.html)

The same software could potentially be used on the ESP32CAM, so for testing the programs I built a development board on stripboard;

<br>
<p align="center">
  <img width="250"  src="/images/ESP32CAM_2.jpg">
</p>
<br>

The connections for the SX1278 LoRa module used were this, and don't forget the antenna;

<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections.jpg">
</p>


The schematic does show ESP32CAM IO4 connected to the SX1278 LoRa device NRESET pin, this can be omitted, just leave the pin floating. The changes needed to the working ESP32CAM 'take a picture and save to SD program' were not major, the significant change was to remove the save to SD stuff and add a function call to the ESP32CAM software that passed a pointer to the array and the length of the array (that is the picture just taken) to a external function like this;

	path.toCharArray(filenamearray, tempsize + 1);
    filenamearray[tempsize + 1] = 0; //ensure null at end of filename
    sendArray(fb->buf,fb->len,filenamearray,tempsize + 1); 

Note that the file name the ESP32CAM software would have used to write the file to SD is also passed across to the sendArray() function. This function is in a library file called ATLibraryIRQ.h and is the same starting point for a data transfer as sendFile(char *DTFileName, uint8_t namelength) would be for a SD file transfer. The ATLibraryIRQ.h can be left in the \src folder of the SX12XX library or copied into the local sketch folder if custom changes are needed. In the case of the ESP32CAM transfer the library file uses the IRQ forms of the data transfer LoRa send and receive functions that do not require the allocation of a DIO pin to detect TXDone and RXDone. It has to be this way since there was such a shortage of pins on the ESP32CAM.  

The program operation is fairly simple, the ESP32CAM wakes from deep sleep, takes a picture, transfers the memory array that is the picture and goes back to sleep, the SD card is not used. 

The receiver program was **239\_StuartCAM\_LoRa\_Receiver** running on an Arduino DUE or you could use **234\_SDfile\_Transfer\_Receiver**. 

Initial tests resulted in poor pictures, way too dark or washed out, so I set the ESP32CAM up using the CameraWebServer example that is within the Arduino IDE, instructions to be found in this useful tutorial; **[https://randomnerdtutorials.com/esp32-cam-video-streaming-face-recognition-arduino-ide/](https://randomnerdtutorials.com/esp32-cam-video-streaming-face-recognition-arduino-ide/)**. You can change the picture settings and capture a still to see the effect. Things improved, garden camera was now working;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_4.jpg">
</p>
<br>

Picture quality is not brilliant, but the image size was only 34,000 bytes or so and do remember ESP32CAMs are fairly cheap. 

I needed a more compact garden cam so used a small PCB which had a LoRa module on it directly, with connectors for programming and a switch for shorting GPIO0 to ground for program upload. 

When assembled onto a PCB the ESP32 CAM and LoRa module is quite compact;


<br>
<p align="center">
  <img width="350"  src="/images/ESp32CAM_5.jpg">
</p>
<br>

The board can replace the AMS1117 regulator with a low IQ current MCP1700 which is also a low drop out voltage regulator so that operation from a single lithium battery is now possible. An alternative, that I have yet to try, is to replace the AMS1117 with a AP7361C.

Without the SD card inserted the deep sleep current of the combination of ESP32, Camera and LoRa module is 1.5mA. The ESP32CAM could, if it were re-designed a bit, reduce that 1.5mA a lot. But the way its wired there is no direct way of powering down the camera. 


### SX128X 2.4Ghz LoRa devices

The UHF SX127X LoRa devices are fine for long distance transfers, but what about shorter distances, how much faster would the 2.4Ghz SX1280 be ? Shorter transfer times would mean longer battery life. What if you wanted to use the ESP32CAM as some form of security monitor, receiving very regular pictures, the duty cycle limits in the UHF ISM bands would likely make it illegal. 

Unfortunately the SX128X 2.4Ghz LoRa devices use a different type of SPI hardware communication versus the SX127X. There is also an additional IO pin, RFBUSY, that needs to be polled to detect when the SX128X device is not busy with a previous command. The ESP32CAM is already critically short of spare IO pins, so what to do ?

If you limit image size to circa 800x600 then you can do without the PSRAM which in turn implies that IO16 could be used. IO4 drives the white LED which you can do without and its easy to remove the driver transistor so the use of IO4 by the LoRa device does not result in the white LED flashing all the time.

Whilst the circuit above for the SX1278 does include NRESET being driven, it does seem happy to operate without it, just leave it floating. However the SX1280 seems to rely on the device being hard reset, via the NRESET pin, before setting up the SPI interface. The device can lock up into a state where the RFBUSY pin is permanently high and only the use of NRESET will clear it. 

The library functions really do need access to the RFBUSY signal so we allocate IO4 as an input to that signal (after disabling the white LED) which now leaves us without a pin to control NRESET if we need to leave IO16 alone. 

One solution is to put an I2C IO expander, PCA9536 for instance, on the same TX and RX pins that are used for program upload. The PCA9536 does not seem to mind there being serial upload activity on its I2C pins or even Serial prints when the sketch is running. All you need to do in setup() is first enable the I2C, use the IO expander to reset the SX1280 via a pulse on the NRESET pin and then carry on the sketch in the normal way. 

I tried the PCA9536 and it worked, I could use one of the four output pins for NRESET, one for an indicator LED and leave the other two for my Mikrobus LoRa modules that may need RX and TX enable pin switching. See the layout below; 


<br>
<p align="center">
  <img width="350"  src="/images/ESP32CAM_7.jpg">
</p>
<br>


The SX1280 does also seem to be sensitive to the state of the SCK\MOSI\MISO pins at NRESET time so I set the SPI pins to inputs before resetting the SX1280 after waking up from a deep sleep.  You also need to set the NSS output pin to high before turning it on as an output, but once all that is sorted the ESP32CAM will wake up from deep sleep, take a picture, transfer it over LoRa and go back to sleep again. 

If the distances are short then you can use the SX1280 in FLRC mode and a typical FRAMESIZE_SVGA (800 x 600) picture takes less than 1 second to transfer, that's around 250 x 127 byte packets of picture data and 250 acknowledge packets all processed and sent and within one second.   

The schematic of the set-up so far for the SX1280 is below, note the LoRa module (MB1) shown is a socket for the plug in LoRa Mikrobus boards I do. 

<p align="center">
  <img width="450"  src="/images/StuartCAM_Schematic.jpg">
</p>

Sketches **240\_StuartCAM\_ESP32CAM** have been added to the Camera example folders for SX127X and SX128X in the [**SX12XX-LoRa Library**](https://github.com/StuartsProjects/SX12XX-LoRa). 

So we have a set of connections that allow the ESp32CAM to reliably use the an SX127X or SX128X LoRa device, but what about the SD card ? 



## Getting the SD card to work

The ESP32CAM has an SD card but unfortunately it uses the same pins that the SPI interface needs for the LoRa device. So there are going to be conflicts and whilst I initially had it working, with the image transfer,  after a few deep sleeps and wake ups the SD card would stop initialising. It had to be removed or otherwise powered down and put back in to recover. 
Clearly it would be helpful if the images taken were also saved on the ESP32CAM SD card.

### UHF SX127X LoRa device

This is the original circuit I had used for StuartCAM with SX127X;

<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections.jpg">
</p>

When we look at the functions of the pins that the ESP32 uses on the SD\_MMC interface to access the SD we see;

<table>
<thead>
<tr>
  <th>ESP32CAM Pin</th>
  <th>SD_MMC Function</th>
  <th>SPI LoRa Function</th>
</tr>
</thead>
<tbody>

<tr>
  <td>2</td>
  <td>DATA0</td>
  <td>MOSI</td>
</tr>

<tr>
  <td>4</td>
  <td>DATA1 (not used)</td>
  <td>NRESET</td>
</tr>

<tr>
  <td>12</td>
  <td>DATA2 (not used)</td>
  <td>not used</td>
</tr>

<tr>
  <td>13</td>
  <td>DATA3 (not used)</td>
  <td>NSS</td>
</tr>

<tr>
  <td>14</td>
  <td>CLK</td>
  <td>SCK</td>
</tr>

<tr>
  <td>15</td>
  <td>CMD</td>
  <td>MISO</td>
</tr>

<tr>
  <td>16</td>
  <td>not used</td>
  <td>not used</td>
</tr>

</tbody>
</table>

The SD\_MMC is used in 1 pin mode so that data or commands are clocked via DATA0 only, DATA1,DATA2,DATA3 are not used. To set the SD\_MMC to this 1 bit mode you initialise it with this function;

SD_MMC.begin("/sdcard", true));

You can see from the table above that the clock pin for the SD\_MMC is the same as the SPI SCK pin so when the SPI for the LoRa device gets used the SD card may well receive spurious commands that confuse it; so perhaps that's why it locks up after a few cycles. 

### GPIO14 SD-MMC CLK

Could we keep the SD\_MMC CLK pin on GPIO14 clear of activity so the SD card behaves more reliably ?

GPIO4 on the ESP32CAM is used by the white LED, so to free up this pin for the LoRa device I removed its switching transistor, see picture below. 

<br>
<p align="center">
  <img width="250"  src="/images/ESP32CAM_GPIO4_Transistor.jpg">
</p>
<br>


We can now assign GPIO4 for the LoRa device SPI SCK and the SD\_MMC CLK pin (on GPIO14) is now clear of interference. I made this change on my stripboard setup and loaded the code up. 

Success ! 

The picture transfer code was run for some 350 wake ups and saved the image to SD card every time and then successfully transfered the image with LoRa as before. Progress.

### GPIO12 and GPIO16

There are now two unused pins, GPIO12 and GPIO16. GPIO16 is the PSRAM select for the ESP32CAM and whilst we could use it for the LoRa device picture size would then be limited to 800x600, larger images require the use of the PSRAM. 

GPIO12 can be used but we need to be sure to leave it floating during programming or reset as its used internally by the ESP32 to sense the  correct voltage for the Flash. An external pull-up of 47K or higher seems to be OK. So we could use it for the LoRa devices NSS as long as we don't put a low value pull up resistor on this pin. I did try 10K, which would be a normal value for an SPI CS pull-up resistor but it produces an ESP32 Flash error on start-up, so for now I removed the pull-up.

MISO was moved from GPIO15 (SD\_MMC CMD) to GPIO13 which is SD\_MMC DATA3 which should be unused.

The pinouts are now, with NRESET not connected;

NSS 12, SCK 4, MISO 13, MOSI 2 

### LoRa device NRESET

Can we get NRESET working, as that would guard against potential lock-ups of the LoRa device ? 

When the ESP32 wakes up at each picture taking the LoRa device is set-up and there should ideally be a short pulse on NRESET before the actual set up. This single pulse on the SD\_MMC CLK pin (GPIO14) might not be an issue so I tried it.

More Success ! 

The picture taking ran for 125 pictures with no SD card lock-ups. 
Note that we are now not using GPIO15, in part this is deliberate since looking to the future the SX128X and SX126X devices need an input pin to read RFBUSY on those devices. 

So if we put the LoRa device NSS on GPIO12 we can leave pin 14 (which is the SD\_MMC CLK) alone. 

### GPIO15 and NRESET

In the specific case of the SX127X we can try and use GPIO15 for NRESET rather than GPIO14 as this would then leave the SD\_MMC CLK free of all potential clocks. GPIO15 is the SD\_MMC CMD pin and I was hoping the odd clock pulse on here would have no effect if the main SD\_MMC CLK pin was not running So that's what I tried tried next and;

Even More Success !  

All the required pins on the LoRa device being driven and the SD card seems to be happy too. 

Here is the now revised pin-out;

<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections_Revised.jpg">
</p>

And the allocated pins for **SX127X** are;

<table>
<thead>
<tr>
  <th>ESP32CAM Pin</th>
  <th>SPI LoRa Function</th>
</tr>
</thead>
<tbody>

<tr>
  <td>2</td>
  <td>MOSI</td>
</tr>

<tr>
  <td>4</td>
  <td>SCK</td>
</tr>

<tr>
  <td>12</td>
  <td>NSS</td>
</tr>

<tr>
  <td>13</td>
  <td>MISO</td>
</tr>

<tr>
  <td>14</td>
  <td>not used</td>
</tr>

<tr>
  <td>15</td>
  <td>NRESET</td>
</tr>

<tr>
  <td>16</td>
  <td>not used</td>
</tr>

</tbody>
</table>

<br>

## 2.4Ghz SX128X LoRa device

So what happens when this new revised pin-out is tried on the SX128X ? Remember the SX128X needs an extra IO pin to ready the LoRa devices RFBUSY pin. The plan here would be to put NRESET on GPIO14 and RFBUSY on GPIO15. Although GPIO14 is the SD\_MMC CLK pin the single pulse on NRESET at start-up did not appear to cause problems when tested with the SX1278 set-up described above. 

So I converted the SX127X sketch (with the SD working) across to SX128X. With StuartCAM running in FLRC mode in my workshop, an 800x600 image was being transferred across to the receiver in around 670mS. Thats a packet around once every 1.7mS. Gosh thats quick.

I left the transfer program running overnight and by morning around 6000 images had been taken and transferred.

All images were received and stored OK on SD, the receiver was an Arduino DUE. However on the transmitter there was a corrupt image at number 2604. Then there were saved images up to 2623, which were also unreadable, and then there were no more images saved. The serial monitor log did not show any SD errors however, so the ESP32CAM thought the SD card was OK. 

I re-formatted the SD cards and swapped them around between TX and RX. This time it was the receiver that had corrupt images after 2052 were saved, the 6,500 images on the transmitter SD were OK. So the failure was caused by the SD card, not a problem with NRESET on the transmitter.  

And the allocated pins for **SX128X** are;

<table>
<thead>
<tr>
  <th>ESP32CAM Pin</th>
  <th>SPI LoRa Function</th>
</tr>
</thead>
<tbody>

<tr>
  <td>2</td>
  <td>MOSI</td>
</tr>

<tr>
  <td>4</td>
  <td>SCK</td>
</tr>

<tr>
  <td>12</td>
  <td>NSS</td>
</tr>

<tr>
  <td>13</td>
  <td>MISO</td>
</tr>

<tr>
  <td>14</td>
  <td>NRESET</td>
</tr>

<tr>
  <td>15</td>
  <td>RFBUSY</td>
</tr>

<tr>
  <td>16</td>
  <td>not used</td>
</tr>

</tbody>
</table>

The workaround for the SX128X if NRESET on the SD\_MMC CLK pin caused problems was to put a PCA9536 I2C IO expander on the program upload pins and use that device at start-up to reset the LoRa device, but it this workaround does not seem to be be necessary.


<br>

## Extra Information

I was reading this ESP32CAM tutorial;

[https://randomnerdtutorials.com/esp32-cam-ai-thinker-pinout/](https://randomnerdtutorials.com/esp32-cam-ai-thinker-pinout/)

And found this comment;

"In order to free pin12 and pin13 (provided you wouldn’t need them for a very demanding SD card usage) you can modify ESP32-CAM fuses like this:
(from Terminal window) $ espefuse.py –baud 115200 –port **your\_USB\_port\_name** set\_flash\_voltage 3.3V
and then include in your Arduino code:
SD\_MMC.begin(“/sd”, true);"

So that might be worth a try one day.

<br>

#### Please note:

**I am not, very definitely not,** in any way whatsoever an 'expert' on the ESP32CAM so will leave the task of refining the settings for taking pictures with and support of the ESP32CAM to others.

<br>

### Stuart Robinson

### November 2021
