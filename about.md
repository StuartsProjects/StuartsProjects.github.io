---
layout: page
---
## About me

Since retiring in July 2017, I can now do stuff that interests me that I did not have time for when working full time. My main interests are in testing the capability of **LoRa®** and designing low cost easy build long distance communication devices. 

I studied Radio Television and Electronics at Llandaff Technical College in Cardiff from 1977 to 1980. In my final year I was awarded the UK national prize for Microelectronics.

From 1980 I worked as a Microprocessor design engineer at Gowerpoint Manufacturing in Cardiff. This company were one of the very early adopters of the use of Microprocessors in amusement machines.  

In 1983 I joined AB Electronics in Abercarn and was responsible for the electronics aspects of the manufacture of **[BBC Microcomputers](https://en.wikipedia.org/wiki/BBC_Micro)**. When the first ARM powered machine in the World (the **[Acorn Archimedes](https://en.wikipedia.org/wiki/Acorn_Archimedes)**) came into the lab for the company to build, we all thought 'gosh it's fast'.

Made redundant in 1993 I set up my own company, SR Networks, installing computer systems and networks for local Cardiff companies. 

From 2005 I worked as a Novell Engineer contractor. In 2008 I was employed by Fujitsu Services who at the time had the contract to support the Driver Vehicle Licensing Agency's (DVLA) network of computer systems in Swansea. In 2015 the DVLA brought their computer network support in-house and I was then a civil servant and retired two years later. 

In 2012 I was asked to provide assistance for an Educational based satellite project being run by Professor Robert Twiggs of Morehead University in America. At the time I had been doing much experimenting with PICAXE microcontrollers and low cost communication devices. The objective of the PocketQube project was to demonstrate that very small and low cost satellites were practical, would have lower launch costs and could form part of the School\College curriculum. 

Myself and two colleagues in the US were invited to submit a completed satellite for launch and $50SAT (as we called it) was put into Earth orbit on 21st November 2013. The design of $50SAT was minimalist on purpose and despite it's very low build cost of £125 it operated successfully in orbit for 20 months. A lot was learned about the conditions that such small satellites, $50SAT weighed only 206grams, must endure in orbit. 

<br>
<p align="center">
  <img width="460"  src="/images/$50SAT_with_can.jpg">
</p>

<br>

If the launch of $50SAT had been 6 months later, then it is likely we would have been able to take advantage of the new LoRa® devices that had been recently released by Semtech. 

I began experimenting with LoRa® in mid 2014 and did some of the early work that established the very long range capabilities of LoRa®. I designed the HABAXE high altitude balloon tracker based on a PIXAXE micro controller and using low cost foil party balloons was able to demonstrate that when used at even the low licence exempt powers (10mW), LoRa® was capable of more than 250km range.

<br>
<p align="center">
  <img width="460" src="/images/January2015_8.jpg">
</p>


**<p align="center">
  HABAXE
</p>
<br>**




At the end of 2018 I started looking at the SX1280 2.4Ghz LoRa® devices and eventually produced a Arduino library for the devices that included the distance measuring (ranging) capability of the SX1280. Used as a tracker in a high altitude balloon flight a record distance of 89km was achieved.

The SX128x library I had written was converted to support the SX126x UHF LoRa devices which have the same type of hardware interface. The SX126X library was adapted for the SX127X devices so that I now had one library that supported SX126X, SX127X and SX128X devices with a common sketch style. This library is open source and can be found here;

**[SX12xx LoRa Library](https://github.com/StuartsProjects/SX12XX-LoRa)**

<br>

**Stuart Robinson
<br>
2020**