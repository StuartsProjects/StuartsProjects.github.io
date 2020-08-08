---
layout: post
title: "Measuring Low Currents"
date: "2018-09-09"
---

You may find you need to measure the current consumption of your latest cunning project.

If this current is in the mA range, say from 2mA up to 500mA or more then a standard multimeter will normally do the job. All you need to do it put the multimeter on a suitable current range in series with the power supply, as in the picture;

![Measuring current Reduced](/images/Measuring-current-Reduced_thumb.jpg "Measuring current Reduced")

One issue that can affect the measurements is the voltage drop across the multimeter. The multimeter measures current by allowing the current to flow through a low value resistor and then measures the voltage drop across that resistor (normally called the shunt). The multimeter can then calculate the current in the resistor.

This voltage drop across the resistor\\multimeter is the **burden voltage** of the multimeter and its consequence is that the project whose current consumption you are measuring is now lower than before.

A Fluke 87 for instance has a burden voltage of 1.8mV per mA on the mA range so if the current flow is 100mA, there is an 180mV voltage drop across the multimeter and the projects supply voltage is now 180mV lower.

What do you do if you want to measure the sleep current of a project which is likely in the tens of uA range? If you have the multimeter on the mA range there won’t be enough resolution to give a good indication of a current in the low uA range.

Also you probably cannot put the multimeter on the mA and then switch to uA as the switching can disconnect power from your project and cause a reset.

So what happens if you start with the multimeter on the uA range? The Fluke 87 has a burden voltage of 100uV per uA on this range so if the project is drawing 100mA when its not in sleep mode the burden voltage of the multimeter will be;

100,000 \* 0.0001 = 10v !

Oh dear, with a 10V voltage drop across the multimeter your project is unlikely to run in at all.

![Wire Short Reduced](/images/Wire-Short-Reduced_thumb.jpg "Wire Short Reduced")

So what to do ?

The answer is fairly straight forward. Start with the multimeter on the uA range and short across it with a bit of wire.

See picture, the multimeter connections are the red and black crocodile clips, the shorting wire is the yellow one.

Then when your project puts itself into low current mode, remove the wire. The burden voltage of 100uV per uA should not cause problems with most projects and you should be able to accurately measure currents down to 1 or 2 uA.

The project shown in the first picture has a sleep current 12.9uA, so the burden voltage is only 1.29mV, which is unlikely to cause a problem. My OWAN B35T (much cheaper than a Fluke !) has a burden voltage of 50uV per uA.

Rather than use a bit of wire to short out the multimeter, make up a switch unit that has power in and out connectors (see picture) with tags for the multimeter connections in the positive line. The switch shorts across the multimeter connections. Short out the multimeter with the switch put the multimeter on the uA range and when the project is in low current mode open the switch. Simple.

![Switch Reduced](/images/Switch-Reduced_thumb.jpg "Switch Reduced")

![Switch in Use Reduced](/images/Switch-in-Use-Reduced_thumb.jpg "Switch in Use Reduced")
