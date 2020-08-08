---
layout: post
title: "The MAX6369-MAX6374 Watchdog"
date: "2016-06-22"
---

This is a marvellous device to fit to a balloon tracker. It is a timed watchdog that needs no extra components to operate, you just provide a pulse every so often and it sits there doing not much at all.

If it does not see a pulse for the watchdog period then it will reset the tracker, recovering it from a possible fatal program crash.

[Data Sheet is here](https://datasheets.maximintegrated.com/en/ds/MAX6369-MAX6374.pdf)

The watchdog timeout can be a range of values from 1 to 3mS up to 60Secs to 180Secs, depending on the particular device and how you connect 3 pins.

I use the 60Sec to 180Sec timeout, this is plenty for a tracker and has the advantage that even if the pulse to the watchdog fails, but the program is otherwise running, you have enough time to read the GPS and send the tracker payload before its reset.

The code I write for my trackers has the routines to pulse the watchdog already embedded so all you need to do if you want a separate independent of the processor watchdog function is to fit this single device.
