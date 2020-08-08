---
layout: post
title: "GPS backup issues 2 – Super Capacitor ?"
date: "2019-01-09"
---

If a small lithium battery is not suitable;  what about a super capacitor that can at least be charged fairly quickly so it might accrue enough charge during a short hot fix power on to last for several hours ?

There is a web site which is an online super capacitor charge and discharge calculator;

[Super Capacitor Calculator](https://www.maximintegrated.com/en/design/tools/calculators/product-design/supercap.cfm)

First lets see what size of super capacitor we need. Assume a maximum backup time of one day, a fully charged voltage of 3.0V and a minimum volts for GPS backup of 1.5V, a  GPSs backup current of 15uA which is  typical for a Ublox GPS for instance. The calculator suggests a capacitor of 1Farad would last a couple of hours longer than one day.

If the hot fix gap was 10 minutes and the hot fix takes 5 seconds with a GPS backup current of 15uA, then assuming no charge\\discharge efficiency losses the charge current needs to be 600/5 times the backup current or 1.8mA. That's manageable.

So if we are to use a 1F super capacitor how big would it be ?

Checking on the Farnell web site a 1Farad 3.6V super capacitor would be around 20mm, perhaps a bit more, in diameter and 5mm high. That’s really quite big, see picture for how big the 5.5V version 1F capacitor is. The GPS shown is one of the larger 25mm ceramic patch antenna types.

 

![GPS and SuperCapacitor](/images/GPS-and-SuperCapacitor_thumb.jpg "GPS and SuperCapacitor")

There are some small super capacitors around the same size as the Seiko MS621, but these super capacitors are of very limited capacity and would only provide a couple of hours backup (based on practical tests).

So if a super capacitor is too big to be practical and we have allready dismissed small lithium rechargeables, then what is the alternative ?
