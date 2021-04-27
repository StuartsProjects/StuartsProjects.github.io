---
layout: post
title: "Antennas for SX1280 2_4Ghz LoRa transceivers"
date: "2019-04-27"
---


### 2.4Ghz Antennas

For the SX1280 we can use readily available and low cost 2.4ghz WiFi antennas.

For long distance use it will be important to know how effectively the various types of antenna actually do perform. If the reception distance can be arranged to be a mere 33m with a simple antenna then the distance would only need to go to 66m for an antenna with a relative gain of 6dB. 

This makes testing 2.4Ghz antennas practical in a large open area such as a playing field. At the highest LoRa data rate 203kbps the working range at lowest power, -18dBm, is quite short, so the SX128x provides an easy way to test 2.4Ghz antennas.  

I fitted an SX1280 module and standard short antenna to one of my Mikrobus based Arduinos;

![Picture 1](/images/Arduinos.jpg)

The transmitter was fitted with another Ebyte SX1280 module on the Arduino board shown above. The '103_LoRa_Transmitter_Detailed_Setup' sketch was programmed to send out a packet about once a second. The LoRa settings used were;

	Bandwidth LORA_BW_1600
	SpreadingFactor LORA_SF5
	CodeRate LORA_CR_4_5
	TXpower = -18 

These settings are the fastest and lowest power so the range should be short, in the hundreds of metres range, this will make testing in a large field practical. 

The receiver was the same board, using the sketch '104_LoRa_Receiver_Detailed_Setup' with a buzzer connected. When a packet was received a packet the buzzer sounded. 

So with the antenna under test fitted to the receiver, I just walked away from the transmitter until the beeps stopped. With the basic antenna (no. 1 in picture) this distance was 82m. See the pictures below of the antennas tested, and then a list of the distances. 

![Picture 1](/images/2_4Ghz_Antennas.jpg)

    Antenna   Type          Distance
    
    1         Vertical      82m 
    
    2         Small panel   112m 
    
    3         Vertical      108m 
    
    4         Vertical      95m 
    
    5         Vertical      87m 
    
    6         Yagi          195m 

So no major differences between the verticals and a small benefit with the panel. The exception was the yagi, going more than twice as far as the basic antenna. The yagi was however, highly directional, as expected really. The distances are proportional to the gain.  

The SX1280 thus provides a very simple and quick way to test WiFi antennas. 


#### Stuart Robinson

#### GW7HPW

#### April 2019**