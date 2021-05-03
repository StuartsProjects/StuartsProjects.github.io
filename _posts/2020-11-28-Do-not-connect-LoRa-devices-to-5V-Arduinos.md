---
layout: post
title: "Do not connect LoRa® devices directly to 5V Arduinos"
---

I am sure that observant LoRaites will have noticed that for the SX127X LoRa devices the data sheets have no maximum voltages stated for the logic pins, SCK, MOSI, NSS etc. So what does that mean, that the pins are 5V tolerant possibly or that it does not matter and you could put many kilovolts on a pin ?

We all may assume these LoRa devices are 3.3V logic devices but some who try it say they do work (for a while at least) when hooked up to a 5V Arduino such as a UNO. 

So I checked what happens when I connected a SX1276 LoRa module to an Arduino UNO (do this at your own risk). The device appears to work, you can read and write to registers etc. However the LoRa device is putting out signals of around 4.2v peak on the MISO pin.  

This is odd I thought, the device is only connected to a 3.3V supply ? 

Turns out the LoRa device is being phantom powered by the 5V outputs of the UNO, NSS and MOSI and this raises the LoRa devices VCC supply to around 4.1V. 

Oh dear ..... the absolute max voltage on the VCC supply pin is 3.9V and the operating range is listed as 3.7V max. 

So thats why you should not connect 3.3V LoRa devices directly to 5V Arduinos. 


**Stuart Robinson
<br>
November 2020**     