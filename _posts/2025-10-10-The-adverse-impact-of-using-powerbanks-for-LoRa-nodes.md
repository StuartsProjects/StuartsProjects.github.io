---
layout: post
title: "The adverse impact of using powerbanks for LoRa nodes"
date: "2025-10-10"
---

## The adverse impact of some power banks

For Meshtastic, which uses LoRa devices, its common to see static nodes that are used for relays and repeaters that are solar powered. If the node is remote, atop a mountain or tall mast, there is often little alternative to solar power. The Meshtastic nodes do not use large amounts of power, especially those powered with an NRF52840 processor, so large solar panels are often not needed.

However there are some consequences to using the type of solar power that powers the LoRa node via its USB interface. Most often this arrangement has a solar panel charging a lithium battery and then uses an up converter to provide the 5V USB output from the nominal 3.7v of the Lithium battery. 

Unfortunately the up converter will typically be a switched mode device operating at a high frequency and its operation can generate significant amount of electro magnetic interference or EMI. LoRa is a technology that allows reception of very weak signals that are well below local RF noise levels. For optimum reception LoRa receivers are best kept away from electronics and the like that is generating EMI.

Using the LoRa link tester software that was previously described in the SX1262 vs LR1121 post;

[https://stuartsprojects.github.io/2025/10/09/LoRa-SX1262-vs-LR1121-for-868Mhz.html](https://stuartsprojects.github.io/2025/10/09/LoRa-SX1262-vs-LR1121-for-868Mhz.html "https://stuartsprojects.github.io/2025/10/09/LoRa-SX1262-vs-LR1121-for-868Mhz.html") 

Its possible to test in a controlled way if a power bank causes reception problems for LoRa. These are the two Solar charging power banks that I had to test, a large one and a smaller one;

<br>
<p align="center">
  <img width="450"  src="/images/101025_1.jpg">
</p>


<br>
<p align="center">
  <img width="450"  src="/images/101025_2.jpg">
</p>

The receiver in place on the testing table is a Lilygo T3S3 with SX1262 LoRa device. I first ran a link test with the T3S3 powered from its own internal 18650 lithium battery;

<br>
<p align="center">
  <img width="450"  src="/images/101025_3.jpg">
</p>

The process was the same as that described for the SX1262 vs LR1121 tests described above. 

There were 3 reception test results, the T3S3 powered from its internal 18650 battery, then the T3S3 powered from the large Solar power bank  and finally the T3S3 powered by the small power bank. The CSV results are below;

    SX1262 Internal Battery CSV,0,0,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,16,15,13,14,11,12,11,9,5,2,0,0,0,0
    SX1262 Large Power Bank CSV,0,0,17,17,17,17,17,17,17,16,16,16,17,17,15,15,12,5,4,1,0,0,1,0,0,0,0,0,0,0,0,0
    SX1262 Small Power Bank CSV,0,0,17,17,17,17,17,17,17,16,17,17,17,16,17,17,16,16,15,16,9,8,1,0,0,0,0,0,0,0,0,0

The CSV results were then copied into the 'Link Tester Graph' as before and the resulting graph looks like this;

<br>
<p align="center">
  <img width="450"  src="/images/101025_4.jpg">
</p>

The large power bank (yellow plot) is having a major affect on reception of weak LoRa packets, its about 9dBm worse than the T3S3 operating on its own battery (blue plot). Thus if you were getting packets from say 10km when the T3S3 was operating from its own battery then if you use the power bank to power it the reception range drops to 3-4km. 

The small power bank (red plot) also has a significant though smaller impact on reception range.   

## The real problem ......

OK, so some power banks can have a significant adverse impact on LoRa reception, but the problem is that apart from carrying out the link test above, you cannot tell which power banks are good and which are bad.

So best advice is probably to avoid powering LoRa nodes from power banks or chargers that power the node via the USB connection, use a direct battery instead.  


### Stuart Robinson 
### October 2025
