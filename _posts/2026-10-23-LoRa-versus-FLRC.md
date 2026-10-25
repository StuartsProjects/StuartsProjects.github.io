---
layout: post
title: "LoRa versus FLRC"
date: "2026-10-23"
---

LoRa is generally considered as long range communication and the long distances achieved require that the on air data rates are slow with large packets having a long air time. 

LoRa comms in the 868Mhz UHF band at spreading factor 12 (SF12) and bandwidth 125Khz have achieved over 800km when there is good clear line of sight between transmitter and receiver. But the on-air data rate is only about 300bps or 37 bytes per second. That is fine for low amounts of data such as sensor readings but not even close to fast enough for sending large amounts of data such as files or images.

UHF LoRa devices normally have a maximum air rate of circa 50Kbs, SF5 at 500Khz bandwidth. But even 50Kbps can be considered slow as its 'only' 6Kbytes a second. 

There has been much interest in the FLRC mode that the newer LR2021 LoRa devices can use in the UHF bands, the FLRC promises on air data rates of from 195Kbps up to 1,950Kbps but this range of higher data rates was previously only available on the SX1280 on the 2.4Ghz band. 

The addition of FLRC for UHF devices sounds good for large high speed transfers but what distances would you get versus LoRa ?

### Simple Ping Testing

The easiest way to compare the distance that various devices and modes of operation can achieve is with a simple ping test. You arrange a transmitter to send a stream of packets and then use a portable receiver to see at what distance packet reception stops. If the receiver has a buzzer or beeper that sounds when a packet is received this is quite an easy test.

You can repeat this ping test for the various LoRa or FLRC modes you are comparing, the differences in the distances you get will provide a link budget dB performance difference between the modes. 

LoRa devices are typically long range performers and a ping test over several kilometres is not that practical. What you need to do is significantly reduce the radiated power from the transmitter so that the packets will only have a range in the 100s of metres. With the radiated power restricted you can then compare the various modes in a small area such as a local playing field. 

<br>
<p align="center">
  <img width="450"  src="/images/PlayingField.jpg">
</p>
<br> 

An advantage of significantly reducing the distance of a ping test is that the test can easily be replicated the World over and the results should be the same. 

The power output of the LR2021 can be reduced to -9dBm, but even at this low power you could have range\distance coverage in the kilometres at the 'fast' LoRa settings.

### The benefit of attenuators

If you use SMA connectors for the antennas you can fit an SMA attenuator to reduce the antenna radiated power even further. This is the range of SMA attenuators I would have with me for ping testing.

    Value - Distance Factor 
    6dB     2
    10dB    3
    20dB    10
    30dB    30
    40dB    100 

<br>
<p align="center">
  <img width="450"  src="/images/231026_1.jpg">
</p>
<br> 

So if when carrying out a comparison I needed to cut the range\distance by a factor of 3, so the test fits in the field I am using, I would fit a 10dB attenuator on the antenna.

To carry out a comparison ping test between LoRa and FLRC I used a basic XIAO based board which would accept plug in radio modules. I have used these plug in Mikrobus compatible modules for several years and they make switching modules between the various boards and Arduino shields I have very easy. 

So I designed a Mikrobus board for the LR2021 modules that SEEED had provided for me to test. This board for the LR2021 was easy to assemble and I soon had some working transmitter and receiver code for the LR2021 in LoRa and FLRC with the RadioLib library. 

The Eagle PCB files for the LR2021 Mikrobus board will be found here;

[https://github.com/StuartsProjects/Devices/tree/master/LR2021](https://github.com/StuartsProjects/Devices/tree/master/LR2021 "https://github.com/StuartsProjects/Devices/tree/master/LR2021")

The Eagle files can be imported into Kicad.  

The assembled LR2021 module plug in boards are pictured below;

<br>
<p align="center">
  <img width="450"  src="/images/231026_2.jpg">
</p>
<br> 

### Board for XIAO Micro controller

My PCB for the XIAO is called the '**Easy Mikrobus XIAO 230926**' and is a simple design. It had a socket for the LR2021 Mikrobus board and also the components that allowed you to plug in an external battery. There are also connections that allow you to solder a wire to the battery pad underneath the XIAO and connect it into the circuit so that the battery would be charged by the XIAO. You can also solder a wire to the EN pad to allow an easy to access switch on the PCB to be used as reset\EN. 

The Eagle files for the '**Easy Mikrobus XIAO 230926**' can be found at the device repository mentioned above.

<br>
<p align="center">
  <img width="450"  src="/images/231026_3.jpg">
</p>
<br>

 
### Testing Practicalities

Out in the field its not really practical to re-program a transmitter receiver combination between LoRa and FLRC modes. Also, and for a good valid comparison, you want to use the exact same hardware for LoRa that you use for FLRC so taking multiple transmitter and receivers to compare is to be avoided. 

I had written some basic LR2021 transmit and receive programs for LoRa and FLRC, they can be found here;

[https://github.com/StuartsProjects/Devices/tree/master/LR2021](https://github.com/StuartsProjects/Devices/tree/master/LR2021 "https://github.com/StuartsProjects/Devices/tree/master/LR2021")

This basic code was expanded so that the modem modes could be selected via switches to use a choice of up to four modes. At power on and reset the program would read two GPIO pins, I used D4 and D5 on the XIAO, and then use the logic state of those pins to decide on whether to initialise mode0, mode1, mode2 or mode3. 

Thus just by using some jumper leads or an attached switch you could easily switch the modes on transmitter and receiver without the need for a re-program. The modem parameters to use were defined in advance in the programs Settings.h file. The switches or links can be shown in use below. The GPIOs are setup as input pull up so they need to be shorted to GND to activate the switch. 

<br>
<p align="center">
  <img width="450"  src="/images/231026_4.jpg">
</p>
<br> 

When doing ping tests its very helpful if the receiver makes a noise when it receives a packet. You can use a single GPIO on the XIAO to beep a piezo buzzer but its also possible to use one of the DIOs on the LR2021. All the available DIOs on the LR2021 were added to a connector at the bottom of the LR2021 Mikrobus PCB so its easy enough to attach a buzzer here with test clips, see the picture below;  

<br>
<p align="center">
  <img width="450"  src="/images/231026_5.jpg">
</p>
<br> 

Unfortunately whilst Semtech built the firmware to allow the LR2021 DIOs to be used as general purpose outputs you cannot use the pins to read external inputs. 

### LR2021 LoRa versus FLRC

So for a first test I wanted to compare the distance achievable for the fastest LoRa versus the slowest FLRC.

The programs used for the test are '**5\_LR2021\_LoRa\_and\_FLRC\_Test\_Transmitter**' and '**6\_LR2021\_LoRa\_and\_FLRC\_Test\_Receiver**' and can be found here;

[https://github.com/StuartsProjects/Devices/tree/master/LR2021](https://github.com/StuartsProjects/Devices/tree/master/LR2021 "https://github.com/StuartsProjects/Devices/tree/master/LR2021") 

For this first test the transmitter and receiver were programmed with two modes.

**Mode0 was FLRC**, frequency 868Mhz, BandwidthBitRate of 260K, CodingRate of 3\_4, DataShaping of 0\_5 and a TXPower of -9dBm. 

**Mode1 was LoRa**, Frequency 868Mhz, Bandwidth 1000Khz, Spreading Factor 5, CodeRate 4:5 and a TXPower of -9dBm. 

The on air datarate of the FLRC would be circa 195Kbps and the LoRa would be circa 100Kbps. 

The buzzer was fitted to the receiver and both transmitter and receiver were powered by small flat Lithium Ion batteries. 

I went to my local playing field, this is the transmitter on a bamboo pole;

<br>
<p align="center">
  <img width="450"  src="/images/231026_6.jpg">
</p>
<br> 

The transmitter was fitted with a 10dB attenuator and set for Mode0 which was the FLRC. When you hold the receiver have your arm out level with your shoulder so that your body is not in the way of the path from transmitter to receiver. I walked away from the transmitter but I got to the edge of the park 300M away and was still receiving packets. 

I changed the transmitter antenna attenuator to a 20dB one and this reduced the range and the distance limit of the FLRC packets was then 186M. I next connected the XIAO D5 pin to GND and restarted the transmitter and receiver so that they were now in Mode1 LoRa. The distance limit of this LoRa was 155M, so in summary the results of the comparison were;. 

**FLRC @ 195Kbps 185M**

**LoRa @ 100Kbps 155M**

The range of the FLRC was 20% further, so a link advantage of circa 1.5dBm. 

The FLRC at its slowest speed capability was 2 times faster than the LoRa at its highest speed setting but there was no range penalty. 

The FLRC settings do have an effective bit rate range going from 195Kbps to 1,950Kbps. This is very interesting and useful as the LR2021 can be used for super long distance using slow LoRa all the way up to very fast FLRC, so a very flexible device indeed. 


### Changing code from LoRa to FLRC

Switching between LoRa and FLRC modes is really very easy with the RadioLib library. 

This is the code for LoRa;

      Serial.print(F("Initializing Module for LoRa... "));
      ConfigLoRa_t config;
      config.frequency = Frequency;
      config.bandwidth = Bandwidth;
      config.spreadingFactor = SpreadingFactor;
      config.codingRate = CodeRate;
      config.syncWord = LoRaSyncWord;
      config.power = TXPower;
      config.preambleLength = PreambleLength;
      int state = radio.begin(config);

And this is the code for FLRC;

      Serial.print(F("Initializing Module for FLRC ... "));
      ConfigFLRC_t config;
      config.frequency = Frequency;
      config.bitRate = BandwidthBitRate;
      config.codingRate = CodingRate;
      config.power = TXPower;
      config.preambleLength = PreambleLength;
      config.dataShaping = DataShaping;
      state = radio.beginFLRC(config);
 
All the rest of the packet handling code stays the same.

### Bandwidth issues

Its worth pointing out that the use of FLRC at high speeds might be an issue in some parts of the World. The bandwidth at 1,950Kbps is 2666Khz and there might not be a legal frequency allocation to allow this bandwidth. 

At the other end of the speed spectrum using the new faster LoRa at 100Kbps could also be an issue as the bandwidth is 1000Khz, but if the bandwidth is a problem then you could swap to the slowest FLRC at an effective bit rate of 195Kbps and a bandwidth of 'only' 300Khz. 

### Summary

By adding FLRC to the LoRa device in the UHF band Semtech have considerably improved the range of potential applications than would be possible with just a LoRa device. 

The fastest LoRa has an on air rate of circa 100Kbps at a bandwidth of 1000Khz. Large bandwidth allocations are often not possible (as in not legal) in the UHF bands. If the UHF FLRC used the same bandwidth as the fastest LoRa then you have a on air data rate capability improved to circa 975Kbps, wow same bandwidth but 10 times the speed.
 
### Coming soon .....

Its going to be very interesting to see what sort of line of sight distance the 2600Kbps high speed FLRC is capable of at both UHF and 2.4Ghz. 

I have noticed that some of the FLRC modes seem susceptible to a lot of packet errors, even when devices are close together. Its possible the errors are due to nearby equipment such as PCs, lights etc, so a test away from lots of other electronics is needed.

A display on the receiver would be very useful for outdoor testing. An I2C OLED is easy to drive and cheap but is not really visible in sunlight, however there is a display that is visible outdoors. 


### Stuart Robinson 
### October 2026


 