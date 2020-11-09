---
layout: post
title: "LoRa® is not Slow !!!"
---

Often when wireless communications is discussed on public forums, it is assumed that LoRa® is slow when compared to other small radio modules but this is not really true as a quick check in some data sheets will tell you. 

Take for instance the FSK RFM22B (Si4432 based) module which is quite a popular small and low cost radio module. The RFM22B will run at up to 256kbps. The fastest an SX1276 based LoRa® module can run is 37.5kbps, so clearly the RFM22B is capable of running at a much higher data rate.

However, whilst I have not tested the range\distance over which the RFM22B will work at 256kbps, I have tested it extensively at a data rate of data rate of around 2kbps. The RFM22B has a stated sensitivity of -121dBm at 2kbps. The RFM69 (SX1231 based) is another popular module that has similar performance characteristics of the RFM22B. The slower the data rate the longer the range\distance that can be achieved, this is true of both FSK and LoRa® devices. 

The RFM22B when using 100mW of RF power at 2kbps will when using simple 1/4 wave antennas only reach about 300m in the urban area where I live, so we are not talking of specialist super long range applications.

The Semtech LoRa® calculator tells us that at a sensitivity of -120dBm the data rate the SX1276 LoRa® device can manage is 12.5kbps. 

So the LoRa® device is around 6 times faster for the same sensitivity..................



**Stuart Robinson
<br>
August 2020**     