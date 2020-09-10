---
layout: post
title: "ESP32 Shield – Adding a LoRa® Device"
date: "2018-07-01"
---

I added the socket headers that allowed me to plug in a LoRa® module. The sleep current went from around 8uA to 1.95mA, something definitely wrong. Checking the current consumption figures in the SX127x datasheet and registers it appeared that the LoRa® device was in standby mode, despite the fact that the commands to put the LoRa® device to sleep were being sent.

I eventually traced this issue to the RESET line on the LoRa® device, when the ESP32 enters deep sleep mode it turns off the IO pins and it appeared that during this turn off it causes the LoRa® device to reset. I removed the ESP32 IO pin (27) from the LoRa® device and the LoRa® device then went into sleep mode correctly. An alternative solution was to set pin 27 to an input prior to the ESP32 entering sleep mode. The LoRa® device has an internal pull up on RESET so it would not be floating.

Although the ESP32, SSD1306, FRAM and LoRa® device were now going into sleep mode, the current was still high, in the region of 290uA, I say ‘region of’ because it was varying.  I recall from the previous build of the shield that the LoRa® device would still consume 300uA or so when in sleep mode, but with some of its inputs (SCK, MISO and NSS) floating, this should not be a surprise. So I added the pull ups in order;

Start 280uA.

MOSI 19uA

SCK 8uA

NSS 8uA

RESET 8uA

So it would appear that pull ups on MOSI and SCK are required, and it makes sense to leave the one on NSS to prevent any oddities if the select pin floats low.

Progress so far is that the shield with an ESP32, SSD1306 display, FRAM and SX127x LoRa® device has a sleep current of 8uA, although it does vary downwards a bit, sometimes going as low as 7.2uA.

Testing the board with and without the SSD1306, I would conclude that the SSD1306 consumes 0.5uA in when in sleep mode.

Testing the board with and without the LoRa® device, I would conclude that the LoRa® device consumes, 0.1uA when in sleep mode.

The partially completed board is below, it can powered from battery or USB power.

![ESP32 Shield with LoRa®](/images/ESP32-Shield-with-LoRa®_thumb.jpg "ESP32 Shield with LoRa®")

**Next: adding the SD card.**
