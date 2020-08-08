---
layout: post
title: "Reducing project sleep mode current to 0.001uA"
date: "2018-01-16"
---

### **The nanoPower**

### **(A Very low current sleep mode power controller)**

 

![nanoPower](/images/nanoPower_thumb.jpg "nanoPower")


This small PCB is designed for putting battery powered micro controller projects into extreme low current sleep mode and turn them back on again up to one month later.

With care an original projects design may allow it to get current consumption down to 10uA or perhaps a bit less in sleep mode. If a project has been designed without low power sleep mode in mind then a complete redesign is likely not an option.

The nanoPower can be used with most battery powered micro controller projects, old or new without changing the design. The nanoPower will reduce battery drain in sleep mode to almost unmeasurable figures, to less than 0.001uA.

The nanoPower is suitable for projects that are battery powered at up to 6V and connects between the battery itself and the projects battery input. There is reverse battery and over current protection. A 4 pin cable is required to connect the nanoPower to the projects I2C interface, 5v or 3.3v, for control. Simple software on the project configures the real time clock (RTC) on the nanoPower. Any sleep time between 1 second and one month can be selected.

On a once per hour wakeup the RTCs lithium backup battery should keep the nanoPower going for a year. There is a power switch on the board so that you can be sure to disconnect the project from the battery if need be.

To use the nanoPower connect it between battery and project as shown in the picture bellow and turn the power slide switch on (up).

### **P****rogramming**

To first use the nanoPower with a project you need to press and hold down the push button, left in the picture. This forces the power on. Keep the button pressed until your initial program has been loaded and run.

Any project using the nanoPower does needs as the first instructions in its program after reset or power up to write to the RTC and configure it to keep the power on.

Sample code has been tested for Arduino and Micropython.
