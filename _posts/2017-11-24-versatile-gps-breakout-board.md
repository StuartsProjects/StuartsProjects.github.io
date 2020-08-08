---
layout: post
title: "Versatile GPS Breakout board"
date: "2017-11-24"
---

![Breakout and TC74](/images/Breakout-and-TC74_thumb.jpg "Breakout and TC74")

 

For high altitude balloon tracking it is common to use a Ublox GPS as these are one of the few low cost GPSs that can be configured to work above 18,000m. The MAX M8Q is a popular choice in particular for the smaller Pico trackers where you want the total weight to be 25g or less.

I revised the layout of my previous GPS breakout board, this new one is a lot smaller and is more flexible.

You can fit a ceramic stick antenna, but a bit of guitar wire is lower cost and produces better signals. There are holes on the board to take either a basic ¼ wire antenna (4.75cm @ 1575Mhz) with ¼ ground radials if you choose. The simple ¼ wave antenna wire shown in the picture works well enough, you can improve the signals further by adding 4 off ¼ radials or increasing the antenna wire to ¾ wavelength.

The breakout board can be used with the Ublox GPS serial or I2C interfaces.

The board has the pads for a SMD TC74 temperature sensor, if you can solder the Ublox GPS in place you can add this. The TC74 uses the I2C interface and for Arduino adding this sensor to a program does not need a lot of extra memory. 

![Breakout and BMP280](/images/Breakout-and-BMP280_thumb.jpg "Breakout and BMP280") 

There is a 4 pin 0.1” header, to which you can fit a number of different I2C sensors. Shown in the picture is the board with a BMP280 pressure and temperature sensor. You can also fit a BME280 or a HMC5883L Magnetometer. These modules are readily available assembled, at prices that are lower than buying the individual parts, check on eBay.

You can fit a 0805 LED if you choose and it will flash once the GPS has a fix.

Make sure you have the power and I2C connections correct depending on where you are connecting the breakout board to, there are different layouts of pins on my Locator2 board when compared to the GPS breakout.

The PCB layout and schematic of the GPS breakout board are in the HAB2 repository on Github;

[https://github.com/StuartsProjects/HAB2](https://github.com/StuartsProjects/HAB2)
