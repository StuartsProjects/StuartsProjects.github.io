---
layout: post
title: "ESP32 Shield - a Low Power Journey."
date: "2018-06-29"
---

This post is about the build of a very low sleep current ESP32 shield. This is my second version of the shield and incorporates improvements discovered the first build. The original shield seemed to be a surprise to the outside World, the power consumption was in the region of 8uA when complete with SSD1306 display, FRAM, TC75 temperature sensor and SX127x LoRa® device.

This blog is a description of the build of version 2 of the shield stage by stage and the measured sleep current as more parts are added.

Sleep currents were measured with a multimeter. I built an adapter that goes inline with the battery with connections for the multimeter and a toggle switch. The switch allows the multimeter to be shorted. Thus with the switch shorting the multimeter there are  no burden voltage issues caused by the multimeter. When the ESP32 goes into deep sleep just flip the switch and read the current (in uA) from the multimeter. With the multimeter measuring a a sleep current of 8uA the voltage across the multimeter was only 0.8mV, which is less than would occur if I was using my uCurrent Gold on the 1mV per uA range.

I started the build by adding fitting the  MCP1700 3.3V regulator. The current consumed with nothing connected to the output of the regulator was 1.6uA, a good starting point.

I fitted the ESP32 and the components required to allow the ESP32 to be programmed via a plug in USB to serial adapter, see picture.

The test program was a version the Andreas Spiess used in the video on deep sleep of the ESP32, see the link below;

[https://www.youtube.com/watch?v=r75MrWIVIw4&vl=en](https://www.youtube.com/watch?v=r75MrWIVIw4&vl=en)

I changed the program so that at  start the LED would flash and the ESP32 would go into deep sleep for 30 seconds, when the LED stopped flashing I knew to flip the switch shorting the meter and take the current reading. 

![ESP32 just module](/images/ESP32-just-module_thumb.jpg "ESP32 just module")

OK, so lets add a SSD1306 OLED. The Adafruit library has an option that in effect turns the display off; ssd1306\_command(SSD1306\_DISPLAYOFF);

The test program writes some text to the screen then flashed the LED and goes into a 30 second deep sleep, this is the result, 7.7uA;

![ESP32_SSD1306](/images/ESP32_SSD1306_thumb.jpg "ESP32_SSD1306")

The ESP32 does have 8Kbytes of RTC RAM that will survive deep sleep, so this could be useful for keeping track of what a program is doing when going into and out of deep sleep, however this RAM will not survive reset or power down, so I prefer to add a FRAM to projects. These FRAMs are like EEPROM but they have a limited life, maybe 1,000,000 writes. FRAMs have a write endurance many times greater, up to 100,000,000,000,000 writes, so can be treated as RAM. I added a MB85RC16PNF 2Kbyte FRAM and measured the sleep current, it was only 0.1uA extra;

![ESP32_SSD1306_FRAM](/images/ESP32_SSD1306_FRAM_thumb.jpg "ESP32_SSD1306_FRAM")

So 7.8uA for an ESP32, SSD1306 display and a 2Kbyte FRAM, looks promising.

**Next:  Adding SPI devices.**
