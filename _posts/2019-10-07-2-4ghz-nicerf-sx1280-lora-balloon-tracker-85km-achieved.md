---
layout: post
title: "2.4Ghz NiceRF SX1280 LoRa® Balloon Tracker - 85km Achieved"
date: "2019-10-07"
---

I have had the basic send and transmit function of my Arduino Library for the SX1280 working for a couple of months, but I was keen to subject the code to a real World test before publishing it. So I built a small GPS tracker (25g) to use on a high altitude balloon.

![](/images/word-image-768x576.jpeg)

The PCB files for a small Arduino Pro Mini based controller that can be used with the NiceRF SX1280 boards are available here;

[**Pro Mini based controller device repository**](https://github.com/StuartsProjects/Devices/tree/master/Easy%20Pro%20Mini)

And the PCB files for the plug in board for the NiceRF SX1280 module are available here;

[**NiceRF SX1280 module device repository**](https://github.com/StuartsProjects/Devices/tree/master/Breadboard%20Friendly%20NiceRF%20SX1280%20Board)

The SX1280 LoRa® devices can calculate distance by measuring the time of flight of a special packet exchange, I wanted to see how far this ranging feature would work, I had previously tested it to 40km in a hilltop to hilltop test.

So I set about converting some balloon tracking code I had originally written for the SX127X LoRa® device to use my SX1280 library. This software runs as a GPS tracker typically using an ATMega328P as on an Arduino Pro Mini. The receiver can also be powered by a Pro Mini, but to run the Micro SD data logging, display and receiver’s GPS requires a processor with more memory so I normally use a version of my LCD receiver that has an ATMega1284P processor.

Changing the code to run on the SX1280, including the remote control functions took a month or more and with it eventually working I added the SX1280 Ranging capability. For this function the remote balloon tracker would transmit a short command that the receiver identifies as a ranging request. The receiver then initiates the ranging packet exchange process and displays the results as a distance.

I eventually had a chance to test the tracker on the 22nd July, weather conditions looked suitable and the small tracker was fitted to a Qualatex 36” foil balloon and launched from the Black Rock picnic site between the two Severn bridges on the West side of the Severn.

I was not using the longest range mode of the SX1280, I chose a LoRa® bandwidth of 406khz as I wanted to avoid potential issues if the tracker got very cold and the TX and RX became more than 25% of the bandwidth apart in frequency. Its possible that further work will show that the lower bandwidth of 203khz, which would give more distance capability, can be used. I am also looking at implementing an AFC capability in the software.

The balloon rose slower than expected and was moving horizontally quite quickly. A 2.4Ghz magnetic mounted antenna on my car’s roof was not receiving strong enough signals to be able to follow the pico in the car, so I stuck to tracking it from a playing field with a cheap Wi-Fi yagi.

![](/images/word-image-1-768x1024-768x1024.jpeg)

The balloon rose to 7903m where it apparently burst and descended. I lost contact with it when it was at 3259m altitude and the **GPS calculated distance was 85.325km, direction 57degrees**. The ranging function measured the distance as 85.133km, so with ranging now known to work at that distance, the flight was a success.

![](/images/word-image-2-768x576.jpeg)

The point of lost contact was over the River Stour near Sutton-under-Brailes.

I hope within the next week or so to be able to send the 2.4Ghz LoRa® tracker to around 30km altitude on a latex balloon launch.

The Arduino library seems to work well, with perhaps some improvements required in the ranging code and an AFC capability. I particular I need to write a note on how I carried out the necessary ranging calibration.

**Stuart Robinson**

**July 2019**
