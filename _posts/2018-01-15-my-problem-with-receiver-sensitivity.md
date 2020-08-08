---
layout: post
title: "My problem with receiver sensitivity and link budgets"
date: "2018-01-15"
---

It's simple, when testing I cannot get even close to the figures manufacturers quote in their data sheets for the sensitivity of their radio devices. Which could be important, quoted receiver sensitivity is often used in calculation link budgets.

Two examples;

Hope RFM22B, which is a Si4432 based ISM band radio module, has a quoted sensitivity of -121dBm, but can you get this in real world tests? I for one could not.

Hope RFM98, which is SX1278 based LoRa module that quotes a sensitivity of -131dBm, I could not get close to that either.

### Hope RFM22B\\Si4432

I was evaluating the RFM22 for a long distance communications project so I was testing how much power I needed to cover a 40km hilltop to hilltop link. The RFM22B is a typical frequency shift keying (FSK) type transceiver.

The hills were above Cardiff (UK) and 40km away in the Mendip hills with the Bristol Channel in between. See the blue line 3 in the picture bellow, then the route profile and Fresnel zone profile.

![Picture1](/images/Picture1_thumb.jpg "Picture1")

![picture2](/images/picture2_thumb.jpg "picture2")

![](/images/picture2.jpg)

![Fresnel Zone](images/Fresnel-Zone_thumb.jpg "Fresnel Zone")

I was using ¼ wave wires on transmitter and receiver, data rate 1000bps. Packet reception was reliable for the the 40km with a transmit power of 100mW, with 50% of data packets being received at 50mW.

You can calculate the signal level the receiver should be seeing since we know the antenna type, transmit power and the free space loss for the distance (-117dBm). I calculated the receiver was seeing signals of **\-96dBm.**

Now there is the problem; at just less that **\-96dBm**, the RFM22B would not receive signals reliably, but the data sheet claims the sensitivity was **\-121dBm**, what happened to the missing **25dBm?**

### Hope RFM98\\SX1278 LoRa

I did the same test, over the same 40km path, with the same antennas, This time using a Hope RFM98 LoRa module, data rate 1042bps. These devices are supposed to be long range, and indeed they were, covering the 40km with a mere 3dBm (2mW) of power, so much better than the 100mW needed for the RFM22B.

I recalculated the received signal level for 2mW, it was **\-****11****4****dBm**. This is the same issue as above, reception stopped at signals lower than **\-****11****4****dBm,** yet the data sheet sensitivity is quoted as **\-131dBm,** for the particular LoRa mode in use, so where has the **18****dBm** gone ?

### The Impact of Noise

Imagine your in a large open field. 100M away a radio is playing, you can just hear the music. You have a radio as well tuned to a different station, so you turn it on. Can you still hear the radio 100m away? Very unlikely, the noise (literally) from your own radio drowns out the much quieter sound from the other distant radio.

Radio receivers work in much the same way, radio frequency noise, which is always present, can drown out very weak signals from far away.

What actually limits the ability of a radio to receive weak signals is most often its ability to discriminate the weak signals from the noise, this is the signal to noise ratio (SNR). Although its often not stated in the data sheets, a lot of FSK receivers (such as the RFM22B) need a signal that is between 5 and 10dB above noise level for successful decoding.

In the UHF band the receiver will typically be seeing around -100dBm to -105dBm of noise, the receiver itself may report this, take an RSSI reading with no signal present. If there is around -100dBm of RF noise present and the receiver needs an SNR of +5 to +10dBm to decode signals then the minimum signal level it needs to see is somewhere between -90dBm and -100dBm. So when the 40km test above identified a signal requirement of -96dBm for reception, that matches the SNR requirement.

Back to the LoRa device. The SNR figures for the various modes are given in the data sheet, for the mode used in the 40km test the quoted SNR is -10dBm. Thus if the noise level is -100dBm to -105dBm, the LoRa receiver would need around -110dBm to -115dBm of signal. So again when the 40km test above identified a signal requirement of -114dBm for reception, that matches the SNR requirement.

### Conclusions?

I don’t know where you might be able to receive signals at the levels that are quoted in device data sheets, somewhere very quite with virtually no RF noise, perhaps out by Pluto?

And of course will using quoted receiver sensitivity give you accurate results when calculating link budgets ?
