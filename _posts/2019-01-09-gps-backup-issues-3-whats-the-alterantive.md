---
layout: post
title: "GPS backup issues 3 – Whats the alternative ?"
date: "2019-01-09"
---

So if we don’t use a a super capacitor or a small lithium rechargeable, then what is the alternative ?

We could of course use a non-recharegeable lithium battery such as a CR1216 but in the holders these have much the same issue as the super capacitor, they are physically large, see picture.

![GPS and Battery](/images/GPS-and-Battery_thumb.jpg)

In addition even a battery as physically big as the CR1216 only has a capacity of circa 25mAhr, so that would only keep the GPS (@15uA backup current) going for 70 days. A tracker based on this setup would need it’s GPS battery replaced around every two months.

The answer to the problem is quite simple really. With the small lithium rechargeable or supercapacitor options, then the backup power for the GPS is actually coming from the projects main battery, so why not use the main battery as the backup power source for the GPS ?

All we need to do is take a 3.3V supply from the project and feed this voltage via a diode to the GPS Vbackup supply. We then need to ensure that the 3.3V supply is present when the project (using the GPS) is in sleep mode. This is not difficult even if the project is using something like a TPL5110 to switch off \\on the battery supply to the project. A low drop out regulator such as the MCP1711 has a quiescent current of 0.6uA, so that can be used to go between the projects battery and the GPS Vbackup supply without having a significant impact on total GPS backup current consumption.

Whilst the solution to providing a long term GPS backup current is simple to implement, very few GPS breakout boards provide access to the GPS Vbackup pin, so you cannot feed in an external backup supply. For this reason I designed my own GPS breakout boards for the Quectel L70 and L80 GPS. These are low cost high performing GPSs with substantially lower power consumption than most other GPS modules, see a detailed report here;

[GPS performance comparisons](https://github.com/StuartsProjects/GPSTutorial/tree/master/GPS%20performance%20comparisons)

 

![L70 and L80 Breakout](/images/L70-and-L80-Breakout_thumb.jpg)

The L80 GPS has a slightly worse power performance than the L70, but it has its own ceramic patch antenna and easy to include in PCB designs and solder in place, I like it. The L70 has a balloon mode, for use up to 80km.
