---
layout: post
title: "StuartCAM - ESP32CAM Getting the SD card to Work"
date: "2021-11-27"
---

The ESP32CAM has an SD card but unfortunately it uses the same pins that the SPI interface needs for the LoRa device. So there are going to be conflicts and whilst I initially had it working, with the image transfer,  after a few deep sleeps and wake ups the SD card would stop initialising. It had to be removed or otherwise powered down and put back in to recover. 

Clearly it would be helpful if the images taken were also saved on the ESP32CAM SD card.

### UHF SX127X LoRa device

This is the original circuit I had used for StuartCAM with SX127X;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections.jpg">
</p>
<br>

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

Here is the revised pin-out;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections_Revised.jpg">
</p>
<br>

And the allocated pins are;

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

So what happens when this new revised pin-out is tried on the SX128X ? The plan here would be to put NRESET on GPIO14 and RFBUSY on GPIO15. Although GPIO14 is the SD\_MMC CLK pin the single pulse on NRESET at start-up did not appear to cause problems when tested with the SX1278 set-up described above. 

So I converted the SX127X sketch across to SX128X. With StuartCAM running in FLRC mode in my workshop, an 800x600 image was being transferred across to the receiver in around 670mS. Thats a packet around once every 1.7mS. Gosh thats quick.

I left the transfer program running overnight and by morning around 6000 images had been taken and transferred.

All images were received and stored OK on SD, the receiver was an Arduino DUE. However on the transmitter there was a corrupt image at number 2604. Then there were saved images up to 2623, which were also unreadable, and then there were no more images saved. The serial monitor log did not show any SD errors however, so the ESP32CAM thought the SD card was OK. 

I re-formatted the SD cards and swapped them around between TX and RX. This time it was the receiver that had corrupt images after 2052 were saved, the 6,500 images on the transmitter SD were OK. So the failure was caused by the SD card, not a problem with NRESET on the transmitter.  

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


### Stuart Robinson

### November 2021
