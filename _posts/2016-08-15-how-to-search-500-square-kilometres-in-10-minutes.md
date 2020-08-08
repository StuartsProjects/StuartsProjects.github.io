---
layout: post
title: "How to Search 500 Square Kilometres in 10 minutes"
date: "2016-08-15"
---

**LoRa Relay - Part 1**

Of all the LoRa programs I have written, the relay program is by far the most significant, the impact it can have on long range locating is monumental, and it’s a very simple program.

![6](/images/6_thumb.jpg "6")

At ground level a ‘lost’ tracker, be it a Radio Controlled (RC) model or high altitude balloon, could be just too far way to receive good signals. What can **very** significantly improve search range is increasing the altitude above ground of your receiver, even a 10M pole can make a big difference, especially in urban areas.

Its often not practical to put a large handheld LoRa receiver at height. A good location in an urban area for a receiver may well be on the roof of a house but how do you get all the kit up there ?

The simple answer is that you don’t have to, you put the relay ‘up there’ instead. The relay listens for packets coming from the ‘lost’ model or balloon and then retransmits them so you can pick them up on your normal LoRa receiver. The relay is small light, and self contained.

Using a bit of string and a weight, a small rubber ball is good a stone is not, its only a few minutes work to get a line over a house or tree and pull the relay up. If you carry a long extensible pole, also useful for rescuing models from trees, put the relay on that, the extra 10M or so of height can make a real difference.

![Part 1 - 2](/images/Part-1-2_thumb.jpg "Part 1 - 2")

With a low cost radio control plane or quadcopter, it’s possible to get the relay to 100M plus above ground very quickly. Take a look at the pictures, with a LoRa GPS tracker left running in my garden, once the plane was flown to about 100M I was able to pick up the trackers transmissions, and hence location, across 12kM+ of urban environment and countryside, C on the map below. In about ten minutes I had covered a search area of 500 square kilometres, imagine how long that would take to search at ground level !!!

![Part 1 - 3](/images/Part-1-3_thumb.jpg "Part 1 - 3")

An alternative to using a RC model to get the relay to altitude is to use a kite, the relay is light enough.

The software for the relay is in the HAB programs folder on the dropbox, set the frequency and LoRa constants to match your normal tracker and receiver settings and turn it on. 

The next article will cover the building of the relay.
