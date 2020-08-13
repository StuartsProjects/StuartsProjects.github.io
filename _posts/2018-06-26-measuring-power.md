---
layout: post
title: "Measuring Power"
date: "2018-06-26"
---

When your using or experimenting with radio devices there is often the need to measure power levels at radio frequencies. This can be the power output of a small radio module, 10mW at 433Mhz or the signal power seen by a receiver, –100dBm. The receiver power is often referred to as the RSSI.

Measuring RF power under 100mW, with accuracy, is not so easy as a lot of RF power meters of the type used by radio amateurs often have a minimum power input in the 1W region.

There are now available some low cost RF power meters, which have a wide frequency range of up to 8Ghz and measure power in dBm from –5dBm to –55dBm. One such is shown in the picture, it was purchased on eBay for around £20.![](/images/Power-Meter-300x280.jpg)

If these meters can measure only from –5dBm to –55dBm, and you want to measure the 2dBm output from a LoRa module you need to use an inline attenuator, 20dB or 30dB for example. With the power meter connected through the attenuator and the LoRa module set to transmit a 2dBm carrier,  if the power meter is accurate, then it ought to display –28dBm. See picture for the setup. The LoRa module is on a plug in breadborad friendly module, so it can easily be moved between different controller boards.

 

![](/images/Frequency-Counter-1024x768.jpg)

And here lies a problem, how do you know your (cheap) power meter is accurate?

The simple way is to take the power meter somewhere and get it calibrated, but I decided it would be more useful to calibrate the transmitted power output of a LoRa module and then keep it as a reference. If I know that a specific LoRa module puts out 9.8dBm when set to 10dBm, I can be keept as a long term reference and as long as I take care when using it, always have an antenna connected for instance, then it ought to be stable for quite some time.

In the centre of Cardiff is Eagle labs;

[https://labs.uk.barclays/locations/cardiff-en](https://labs.uk.barclays/locations/cardiff-en)

It’s run by Barclays and Legal & General with RadioSpares providing equipment in the electronics lab. Part of this equipment is a spectrum analyser (RSSA PRO 3032X) and it’s got a  proven calibration history. So my plan was to book a session at Eagle labs and use their spectrum analyser  to ‘calibrate’ the power output of a 434Mhz and 868Mhz LoRa module.

I wrote a simple test program that for the 433Mhz and 868mHz modules that transmitted RF carrier for a couple of seconds at 17dBm, 10dBm and 2dBm, I used a 20dB attenuator between the LoRa module and the spectrum analyser.

I got these results for 434mhz;

LoRa device programmed power output       17dBm          10dBm               2dBm

Measured power 434mHz                            -4.6dBm        -11.3dBm          -18.6dbm

LoRa device actual power 434mHz              15.4dBm         8.7dBm            1.4dBm 

 

For the 868Mhz tests I have included the readings of the power meter with a total of 30dB of attenuator in line, these were the results;

LoRa device programmed power output       17dBm           10dBm               2dBm

Measured power 868mHz                            -4.1dBm         -10.3dBm          -18.5dbm

LoRa device actual power 868mhz               15.9dBm         9.7dBm             1.5dBm 

Power meter indication (30dB att)                -13.1dBm       -19.3dBm           -27.6dBm

Power Meter corrected  +29dBm                   15.9dBm        10.7dBm             2.6dBm 

 

The key point from the above measurements is that I know that my 868mHz module puts out 15.9dBm when set to 17dBm. 

With a 30dB attenuator in use the power meter indicates –13.1dBm. Now 30dBm (because of the attenuator) below an actual power of 15.9dBm is –14.1dBm, so with the power meter actually reading –13.1dBm, its reading 1dBm lower than it should be. The power meter has an offset you can set, so if I set the offset to 29dBm, then when the actual power is 15.9dBm, the power meter will read 15.9dBm due to the effect of the attenuators. 

I know that all sounds a bit long winded, but the end result is that I have a relatively cheap power meter (£20) that has been adjusted to give accurate results. Of course its entirely possible to calibrate the power meter directly, but since I now have a LoRa module that puts out a known amount of power I can keep it and use it to check this or other power meter or attenuators in the future. And then of course with enough attenuators in line, I can also use the calibrated LoRa modules to check the RSSI readings of various radio receivers.
