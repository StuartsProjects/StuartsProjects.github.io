---
layout: post
title: "Reducing Tracker node sleep current a wasteful race to the bottom ?"
date: "2019-02-02"
---

There is quite a lot of significance being attached to reducing the sleep current of LoRa IOT type projects or nodes to extremely low levels, the lower the better apparently, but is sleep current alone a significant factor ? 

A bare bones ATMega328P or 1284P based node with a LoRa module can be used in normal deep sleep mode and the sleep current is around 0.2uA, you can wake up from an external interrupt. Add a regulator such as MCP1700 if you need one, and total sleep current is around 1.5uA with a TPL5010 acting as a watchdog and interrupt wake up device.

A popular alternative to the processor deep sleep approach appears to be to use the TPL5110 device to completely power down a node, in periods from seconds to 2 hours. This set-up has a deep sleep current of less than 0.1uA. This appears to be a significant improvement on the 1.5uA of a bare bones ATMega328P in deep sleep mode, or is it ? Is concentrating or being obsessed with the deep sleep current the best way of reducing overall power consumption  ?

Take a look at the scope plot below, it shows (top trace) the power to a The Thing Network (TTN) GPS tracker node based on an Arduino 8Mhz Pro Mini being turned on. The lower trace shows a logic pin being sent high at the point the tracker node software queued the packet ready to send, so the difference is the node wakeup time. The delay from power on to ready to send the packet was 1512mS, mainly caused by the Arduino boot loader delay. Power consumption during this 1512mS was 5.42mA.  



![Picture 1](/images/BootloaderPowerUp.jpg)

Now compare a similar situation where the tracker node is put into deep sleep and woken up by an interrupt timer such from the TPL5010. The bare bones Arduino with decent regulator and LoRa device has a deep sleep current of circa 1.5uA. Note carefully the time it takes to wake up from deep sleep and be ready to send another packet;

![Picture 1](/images/DeepSleepWakeUp.jpg)

Here the top trace the interrupt waking the tracker node from deep sleep and the bottom trace shows the same logic pin going active just before packet transmission. The delay here is a meagre 4.5mS. 

So the deep sleep wakeup is very much quicker (4.5mS versus 1512mS) but does the quicker ‘boot’ time save power?

Its easier to consider the numbers here in terms of uA/Seconds rather than mAhr and look at the numbers over a day. Lets assume our tracker node sends a transmission every 10 minutes or 144 times a day. 




### TPL5110 Power Down Node

The TPL5110 equipped node (with power down) has a sleep current of 0.1uA so uses 0.1 x 60 x 60 X 24 = 8640uA/Seconds per day during sleep.

At every wakeup it uses 1.512 x 5420 = 8195uA/Seconds per wakeup.

There are 144 of these transmissions, so that is 1180080uA/Seconds per day. 

So our TPL5110 equipped node uses 8640 + 1180080 = 1188720uA/Seconds per day sleeping and waking up,  or 0.33mAhr per day.




### TPL5010 Deep Sleep Interrupt Wakeup Node

A TPL5010 equipped node (interrupt wakeup) has a sleep current of 1.5uA so uses 1.5 x 60 x 60 x 24 = 129600uA/Seconds per day during sleep.

At every wakeup it uses 0.0045 x 5420 = 24.4uA/Seconds per wakeup.

There are 144 of these transmissions, so that is 3513uA/Seconds per day.

So the TPL5010 node uses 129600 + 3513 = 133113uA/Seconds per day sleeping and waking up,  or 0.037mAhr per day. So although the TPL5010 has a higher sleep current over the TPL5110 power down node, it uses far less power. 




### Arduino Boot Loader

Of course we don't have to use the Arduino boot loader to load sketches, we can use an ISP programmer although its less convenient. 


![Picture 1](/images/NoBootloaderPowerUp.jpg)


Here the delay between power applied and ready to send a packet is much less; 120mS. 


### Other Advantages of the TPL5010?

The TPL5010 can act as a watchdog device, if you don't respond to the periodic interrupt in a timely fashion then there will be a reset of the processor, which is useful protection against software crashes or power supply induced errors. 

One additional advantage capitalises on the Arduinos quick wakeup from deep sleep. At 4.5mS it becomes feasible to set the TPL5010 to say a one minute interrupt wakeup and then count the interrupts to get in effect a programmable wakeup time. A simple loop counting 10 of the interrupt wake ups then gets you a 10 minute wakeup, without the need to change the TPL5010 timing resistor. 



### Tracker node sleep current - Conclusion?

Using the TPL5110 to power down node a node (with Arduino serial boot loader installed) does give you a very low headline grabbing sleep current of maybe 0.1uA or even less but because of the extended wakeup times this can equate to 0.33mAhr per day sleeping and waking. 

With the processor powered in deep sleep using a TPL5010 as an interrupt wakeup timer use far less, 0.037mAhr per day, simply because the start-ups are very much quicker. So they are significant power savings to be had using a bare bones Arduino set-up and the TPL5010 as a wakeup timer.  If you remove the Arduino serial boot loader then there is not a lot to choose between the two methods, although the TPL5010 does provide added flexibility. 

There is a far more flexible option than the TPL5110 approach, you can build a bare bones ATMega based tracker with LoRa device and use a DS3231 real time clock as a wakeup timer, this provides programmable wakeup times from seconds to one month. You also have the benefit of an accurate real time clock on the tracker. A prototype tracker set-up this way running from a single LifePO4 (3.6V) battery has a sleep current of 0.9uA. 


## Stuart Robinson
## February 2019
 