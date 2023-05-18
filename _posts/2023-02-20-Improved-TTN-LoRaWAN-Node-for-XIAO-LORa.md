---
layout: post
title: "Improved TTN LoRaWAN Node for XIAO LoRa"
date: "2023-02-20"
---


<img align="right" width="250"  src="/images/020123_12A.png">

Despite being very easy to build, this board is a fairly powerful LoRaWAN node, ideally suited for long term battery use which is also very small and light; only 6g + sensor + battery. Can now be fitted with a FRAM non-volatile memory for saving OTAA session state. Can be used for point to point LoRa too.

<br>
<br>

----------

<br>

<p align="center">
  <img width="450"  src="/images/020123_12.jpg">
</p>

<br>

<br>
<p align="center">
  <img width="450"  src="/images/020123_13.jpg">
</p>

<br>

There was a TTN\LoRaWAN node program published for the previous board, but that program did not have the advantage of a FRAM to save the OTAA session state. Thus if the board was powered down (to change the battery perhaps) or was otherwise reset, it would re-do the OTAA join and this is not very helpful. 

So the original board was changed to take a FRAM. This is a surface mount part, but easy to solder, its the IC on the left of the LoRa module in the bottom picture below. The board length increased from 39mm to 41mm, this new board is the **Board for XIAO LoRa V2**, or the **020123** version. 


The **Board for XIAO LoRaV2** is small and can be built very easily with a minimum of parts. A working TTN\LoRaWAN node can be built using the XIAO SAMD21, LoRa module, PCB, FRAM, battery and some wire. You can add a simple BME280 I2C temperature, pressure and humidity sensor as shown below;


<br>
<p align="center">
  <img width="450"  src="/images/020123_16.jpg">
</p>
<br>

In the boards device repository, there is an example Node sketch for using the BME280 I2C sensor, which also reads the the battery voltage and sends the payload into TTN. The received payload on the TTN console looks like this;

<br>
<p align="center">
  <img width="750"  height="50" src="/images/020123_14.png">
</p>
<br>

The XIAO SAMD21 is configured to use deep sleep mode between transmissions, the internal RTC of the SAMD21 is used to set the sleep time and the current used in that period is circa 10uA, so battery life is good. 

### TTN Fair usage policy

When the node starts up it will use SF12, but if its close to a gateway this should soon drop down to SF8 or SF7. The initial period between transmissions can be configured by the sketches TX_INTERVAL parameter, which is initially set to 60 seconds. After a number of transmissions the sketch will impose the TTN fair usage policy of 30 seconds air time per day by adjusting the sleep time.

### CayenneLPP Payload

The transmitted data is sent in CayenneLPP format and the TTN console payload formatter for the device can be set to CayenneLPP so that you get a direct view on the TTN console of the data sent, battery volts, pressure, humidity and temperature. You can setup the device to forward the data to the on-line service myDevices, which will accept the CayenneLPP format data.


### Weights

Bare PCB weight 1.25g. 

Built board weight, XIAO and LoRa module with wire antenna, BME280 and components to read supply voltage is 8.14g + battery of choice. 

### Getting the Board

The bare 020123 board can be purchased on Tindie here - **[https://www.tindie.com/products/29426/ ](https://www.tindie.com/products/29426/)**

The device repository with example programs. build instructions and layout diagrams are here -[ **https://github.com/StuartsProjects/Devices/tree/master/Board\_for\_XIAO\_LoRa\_V2**
](https://github.com/StuartsProjects/Devices/tree/master/Board_for_XIAO_LoRa_V2)

### Enclosure 

Netweaver has produced a model for a printing an enclosure for the board and a 18650 Lithium battery, you can down load it here;

 [**https://www.printables.com/model/400065-stuartprojects-loraseeed-xiao-samd21-fram-pcb-v2-1**
](https://www.printables.com/model/400065-stuartprojects-loraseeed-xiao-samd21-fram-pcb-v2-1)


<br>
<p align="center">
  <img width="450"  src="/images/020123_15.png">
</p>
<br>


## Important !

You will need to be familiar with using the TTN console for setting up devices for OTAA authentication and for your part of the World, if you are to use the board for TTN. You will also need experience of setting up Arduino sketches that use LMIC for connections to TTN.

Producing a detailed list of instructions, for using the TTN console, to apply to all parts of the World for setting up a device for TTN is beyond the scope of a part (the board) that costs only £1.


### Stuart Robinson

### February 2023