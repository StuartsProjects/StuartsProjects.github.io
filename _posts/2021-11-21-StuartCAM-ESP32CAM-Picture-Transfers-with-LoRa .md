---
layout: post
title: "StuartCAM - ESP32CAM Picture Transfers with LoRa"
date: "2021-11-21"
---

The ESP32 CAM is a low cost device for what you get get, an ESP32, a camera, power regulator and an SD card holder all in a fairly small and light 8g PCB;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_1.jpg">
</p>
<br>


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

The connections for the SX1278 LoRa module are thus, and don't forget the antenna;

<br>
<p align="center">
  <img width="450"  src="/images/ESP32CAM_LoRa_Connections.jpg">
</p>
<br>

The schematic does show ESP32CAM IO4 connected to the SX1278 LoRa device NRESET pin, this can be omitted, just leave the pin floating. The changes needed to the working ESP32CAM 'take a picture and save to SD program' were not major, the significant change was to remove the save to SD stuff and add a function call to the ESP32CAM software that passed a pointer to the array and the length of the array (that is the picture just taken) to a external function like this;

	path.toCharArray(filenamearray, tempsize + 1);
    filenamearray[tempsize + 1] = 0; //ensure null at end of filename
    sendArray(fb->buf,fb->len,filenamearray,tempsize + 1); 

Note that the file name the ESP32CAM software would have used to write the file to SD is also passed across to the sendArray() function. This function is in a library file called ATLibraryIRQ.h and is the same starting point for a data transfer as sendFile(char *DTFileName, uint8_t namelength) would be for a SD file transfer. The ATLibraryIRQ.h can be left in the \src folder of the SX12XX library or copied into the local sketch folder if custom changes are needed. In the case of the ESP32CAM transfer the library file uses the IRQ forms of the data transfer LoRa send and receive functions that do not require the allocation of a DIO pin to detect TXDone and RXDone. It has to be this way since there was such a shortage of pins on the ESP32CAM.  

The program operation is fairly simple, the ESP32CAM wakes from deep sleep, takes a picture, transfers the memory array that is the picture and goes back to sleep, the SD card is not used. 

The receiver program was **239\_StuartCAM\_LoRa\_Receiver** running on an Arduino DUE or you could use **234\_SDfile\_Transfer\_Receiver**. As yet I have been unable to get the SD card working in the same ESP32CAM transmitter set-up, there appears to be too much interference on the SPI bus from the LoRa device and the SD card soon fails. 

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
  <img width="350"  src="/images/ESP32CAM_5.jpg">
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


<br>
<p align="center">
  <img width="450"  src="/images/StuartCAM_Schematic.jpg">
</p>
<br>

Sketches **240\_StuartCAM\_ESP32CAM** have been added to the Camera example folders for SX127X and SX128X in the [**SX12XX-LoRa Library**](https://github.com/StuartsProjects/SX12XX-LoRa). 


#### Please note:

**I am not, very definitely not,** in any way whatsoever an 'expert' on the ESP32CAM so will leave the task of refining the settings for taking pictures with and support of the ESP32CAM to others.

<br>

### Stuart Robinson

### November 2021
