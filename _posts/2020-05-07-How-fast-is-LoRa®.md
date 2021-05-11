---
layout: post
title: "How Fast is LoRa®?"
date: "2020-05-07"
---

Not such a strange question, the 'LoRa® Modem Calculator' tool, shown below, that Semtech provides allows you to get a figure for the ‘Equivalent Bitrate’, but how close can you get to this in practice?

![](/images/lora_Calculator.jpg)

The practicalities are that the transmission of a packet needs data to be loaded into the LoRa® device, then its configured for transmission and then you start transmission, monitor the completion of transmission and finally go around and repeat the operation.

How much does the packet size affect the achievable data throughput and how much will a faster processor improve throughput?  

I wrote some test programs for the SX12XX library so I could easliy configure them for different packet sizes and LoRa® settings. The program would time how long it took to send a series of packets and work out the effective bit rate. There would be an option to require an acknowledge from a remote receiver before sending the next packet. Requiring an acknowledgement for each transmission would of course slow things down as the LoRa® devices would need to swap configurations between transmit and receive all the time, but how much would it slow down ?  

I decided to test throughput for packet sizes of 16 bytes and 255 bytes, and to use these processors;  

ATmega328P running at 8Mhz clock speed  
SAMD21 running at 48Mhz clock speed  
STM32 running at 72Mhz clock speed  
ESP32 running at 280Mhz clock speed.

I tested the SX127X first and rather than setup for Spreading Factor 6 (SF6) fixed length packets, which would have been slightly faster, I used SF7 variable length packets and a bandwidth of 500khz, see the section on the SX126X series devices below on ways to improve throughput further. The two programs are in the [SX12XX library](https://github.com/StuartsProjects/SX12XX-LoRa), '42\_LoRa\_Data\_Throughput\_Test\_Transmitter' and '43\_LoRa\_Data\_Throughput\_Acknowledge\_Receiver'. The results in bits per second throughput are tabulated below;

![](/images/How_Fast_is_LoRa_Results.jpg)

As expected, the shorter packets have a greater percentage of overhead in relation to packet size, and the throughput is lower.

  
The much faster processor on the ESP32 does improve throughput over the 8Mhz ATMega328P, but the speed gain is marginal, considering that the ESP32 has a clock speed 30 times greater. Indeed the faster ESP32 does not run faster when there is a requirement for an acknowledge.

One anomaly is that the STM32, in the form of a XNucleo F103RB board, is actually slower than the ATMega328P, despite a clock speed that is 9 times higher.

A clear conclusion is that the humble 8Mhz ATMeag328P is no slouch, data transfer wise, when compared to the faster processors. Possibly the best option is the SAMD21, fastest processor when used with acknowledge, and close in performance to the ESP32 which is a far more power hungry device.

### Power consumption  

There is also the issue of how much power\\current the various processors consume during operation. The ATMega328P and ESP32 were in a bare bones format so the power consumption was easy to measure, transmit power was set at 14dBm;

  
ATMega328P,idle, 6.5mA, transmitting, 50.94mA  
ESP32,idle,38.8mA,transmitting, 97.1mA.  

The SAMD21 and STM32 will present an interesting challenge for measuring power, as I did not have them in a bare bones format. The SAMD21 was an Arduino Zero Pro board and that has a fair few superfluous parts, which consume power, so for an accurate comparison I need to build a bare bones version of the SAMD21 (in progress). The same applies to the STM32.

### Packet Rate  

Sometimes its the rate at which a packet can be sent is important and as the data throughput for the various processors was similar, by implication the packet rate is similar for the different processors too. The packet rates found when using the ATmega328P @ 8mhz were;

  

> 4 byte packets, broadcast with no acknowledge, 115 packets a second  
8 byte packets, broadcast with no acknowledge, 100 packets a second  
16 byte packets, broadcast with no acknowledge, 72 packets a second  
64 byte packets, broadcast with no acknowledge, 32 packets a second  
255 byte packets, broadcast with no acknowledge, 10 packets a second

### SX1262

The SX126X series LoRa® devices, SX1261, SX1262 and SX1268 introduced the use of spreading factor SF5 which improves data throughput considerably. At SF5 and a bandwidth of 500khz throughput rises to **52,654bps**.  
In a comparison for packet rate, mentioned above, the 4 byte packets which could be used for remote control processes for instance, can be sent at a rate of **180 packets a second**, using only a humble 8Mhz Arduino Pro Mini.

**Stuart Robinson  
May 2020**
