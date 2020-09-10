---
layout: post
title: "Library for SX12XX LoRa® devices"
date: "2020-01-30"
---

For some time I had thought that an Arduino library for LoRa® that included useful application examples would be helpful for those intersted in working with LoRa® devices.

The Semtech SX127x LoRa® devices are well supported already with several Arduino libraries but often the examples are limited in function. The newer SX126x (UHF) and SX128x (2.4Ghz) LoRa® devices are not compatible with the standard SX127x libraries so having already produced a library for both the SX128x and SX126x devices I set about writing a library for the SX127x in the same style as the SX126x and SX128x devices. A common library structure and set of function calls across all the LoRa® devices should mean that an application written for say the the SX1276 LoRa® device @ 434Mhz can easily be adapted for the SX1280 @ 2.4Ghz.

The first part of the library, supporting the SX127x devices is completed and the link is below. Support for SX126x and Sx128x will be added in the coming weeks, using the example programs to ensure the libraries are compatible.

[**https://github.com/StuartsProjects/SX12XX-LoRa®**](https://github.com/StuartsProjects/SX12XX-LoRa®)

The provided examples provided includes sketches for;

A basic register print test

Transmitter and receiver of a character array; "Hello World" etc

Transmit and receive a data structure with a mix of variable types.

Transmit and receive a mix of variable types, bytes, integers, floats and character arrays by writing direct to the SX127x buffer.

A link test transmitter and receiver.

A packet logger.

A received packet frequency error check.

A LoRa® RSSI receive meter with display.

Transmitter and receiver for remote control of devices such as LEDs. Tranmitter can be handheld with a low sleep current of 2uA.

Joystick transmitter and receiver for remote control of RC servos.

BME280 low sleep current sensor transmitter (7uA) and receiver with display.

GPS tracker transmitter and receiver with display.

The example programs will all fit comfortably in a Atmel ATMega328P as used on an Arduino Pro Mini. A simple to build board based on a 3.3V 8Mhz Arduino Pro Mini to which LoRa® modules can be plugged in was developed, see the picture of the board below. There is a fuller description of the board in the 'Evaluation Board' document in the library.

![](/images/Easy_Assembled.jpg)

Easy DIP 231219

The board complete with a LoRa® modules plugged in and being used as a GPS tracker is shown below;

![](/images/Easy_GPS_Tracker-1024x750.jpg)

Currently the only tested versions of the programs are for the ATmega328. I normally change the standard bootloader to the one supplied with the MiniCore core for the Arduino IDE; [https://github.com/MCUdude/MiniCore](https://github.com/MCUdude/MiniCore) This bootloader allows the Watchdog to work correctly and provides a few more bytes of program memory. Several of the example programs have already been tested and work on a small ESP32 board I built. After further testing these programs will be added to the examples.
