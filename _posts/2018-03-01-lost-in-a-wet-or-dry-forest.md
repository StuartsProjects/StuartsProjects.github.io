---
layout: post
title: "Lost in a (wet or dry) Forest"
date: "2018-03-01"
---

So just how far will LoRa transmissions go in a forest that is wet?

I decided to find out.

Local to me is a park that has in the middle of it an area of forest and undergrowth around 300M x 200m. I wanted a location where I could create a reference test, so at one end of the forest, the left mark in the map below, I placed a stick on the ground in the undergrowth and held it in place with pegs. I would use this to line up the tip of the antenna on the test transmitter. Thus in subsequent tests the antenna would be in the same location.

  

![](/images/Wet-or-dry-forest-pic1_thumb.jpg)

On the map the test route distance between the two marks is 200m. The forest is fairly overgrown with a lot of low level vegetation. Below are some pictures of the ‘forest’ so you can appreciate the depth of the undergrowth. The first picture below is from the transmitter location on the left of the map looking towards the receiver location on the right.

![](/images/Wet-or-dry-forest-pic2_thumb.jpg)

The second picture, below, is from the receiver looking towards the transmitter.

![](/images/Wet-or-dry-forest-pic3_thumb.jpg)

This third picture is the test transmitter, an Arduino Pro Mini and RFM98 LoRa device placed in a metal box. A ¼ wave wire antenna is used for the transmitter and receiver. The transmitter is shown bellow with a 10dB attenuator in place to reduce the signal from the antenna to both legal levels, 10dBm in the UK, and to provide a practical range for the receiver. The test transmitter is on the ground amongst the vegetation (worst case location perhaps). The receiver was held in my hand with the tip of the ¼ wave wire antenna at around 1.5M from the ground.

![](/images/Wet-or-dry-forest-pic4_thumb.jpg)

The test method involves sending a series of packets at descending power, 17dBm to 2dBm. The power level used to send the packet is part of the data in the packet. The receiver can then tell the power used to send the packets it is receiving. So if the receiver stops receiving packets at 10dBm, you have quantified how much power is needed to cover the distance.

The LoRa settings were, 434.4Mhz, bandwidth 62.5khz, spreading factor 8, code rate 4:5, explicit mode. This represents a data rate of 1562bps.

At the time of the test its had been raining overnight and for most of the morning. It was raining heavily during the test, the forest was very wet. The test transmitter had a 10dB attenuator in place.

Packets sent at 6dBm power were the lowest received, so taking account of the 10dB attenuator, the packets were in effect being transmitted at -4dBm or 0.4mW.

The first test was with the forest fairly wet and a couple of weeks later it had been dry for several days so I repeated the test with the same equipment with the antennas in the same position and location. This time the packets were just received at -9dB, so the dry forest was 5dB better than the wet. Or more simply when the forest was wet, signals were reduced by around 5dB.

So big question, if the transmissions had been at 10mW (normal ISM band limit) then how far might the signals propagate in worst case (wet) conditions?

If packets are sent at 10dBm but -4dBm is enough to cover the 200m then there is a link margin at 200m of 14dBm. Thus if the forest is assumed to be uniform an extra 14dB on link gain should increase the distance by 5 times, or to 1000m.

If the full 17dBm (50mW) of the transmitter was used the distance covered could increase to 2200m.

There is more, for this test the signals were being sent at a LoRa data rate of 1562bps, if the data rate was lowered to 150bps, which is fine for a lot of data gathering and control applications, range would increase by a further 3 times when using the same power.
