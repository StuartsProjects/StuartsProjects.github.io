---
layout: post
title: "The problem with co-ax cable connections to antennas"
date: "2018-02-22"
---

I have been experimenting and testing some simple antennas for 868Mhz, primarily for Internet of Things (IOT) applications.

A dipole used vertically has around the same performance as a 1/4 wave vertical with 4 x 1/4 radials, but the dipole is easier to build. In the test setup shown below I am using a LoRa® module as a transmitter and measuring the RF field strength it produces at some distance.

![Dipole and Co-ax 1](/images/Dipole-and-Co-ax-1_thumb.jpg "Dipole and Co-ax 1")


The dipole is built on a BNC chassis socket and connected to the radio module module is mounted direct onto the antenna using some adapter, I did not quite have the right one so there is a BNC plug to plug in there. I will re-test when I have the right adapter, but the objective of the test was really to see what we can do to reduce losses when a co-ax cable is used to connect the antenna.

Mounting the module direct on the antenna like this is often not going to be convenient especially if the antenna is going outside.

![Dipole and Co-ax 2](/images/Dipole-and-Co-ax-2_thumb.jpg "Dipole and Co-ax 2")

So what happens when a length of co-ax is used to connect the antenna as shown in the second picture ?

The co-ax cable (which is 0.5M long) outer now becomes part of the antenna and acts to detune it. Compared to the setup in the first picture the co-ax causes the antenna output to drop by circa 5dBm, a significant loss. Whether this can be mitigated by retuning the antenna will be the subject of another article.

![](/images/Dipole-and-Co-ax-3_thumb.jpg)
  
So we are using the co-ax but the antenna performance drops significantly, what do we do about it ? One simple option is to use a clamp on choke of the type you see on monitor cables or power supplies.

The ferrite choke acts to stop RF travelling down the co-ax so it does not detune the antenna so much. The test result was that the transmitter was only putting out 1dBm less than in the first picture, which is a significant improvement over the 5dBm loss without the choke in place.

I will be checking some other clamp on chokes to see if the same results occur with low cost chokes and longer cables too.

