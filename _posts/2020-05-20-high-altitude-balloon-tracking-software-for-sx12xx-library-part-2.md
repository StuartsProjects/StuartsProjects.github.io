---
layout: post
title: "High Altitude Balloon Tracking Software for SX12XX library – Part 2"
date: "2020-05-20"
---

### HAB tracker transmitter options

In part 1 the tracker transmitter has only been used in standard mode, sending the full HAB payload with LoRa only. There are options that can be enabled that add different transmission modes, the first of these is FSK RTTY.

### FSK RTTY - Frequency Shift Keyed Radio Teletype

The SX126x and SX127x series LoRa modules can be persuaded to generate a frequency shift keyed (FSK) carrier which can then be moved quickly in frequency from low to high and this is the basis of single side band FSK RTTY communication. If the carrier is shifted from say a centre frequency upwards by 500hz and back to centre again, then if the single side-band receiver is tuned correctly two audio tones are generated. The low tone represents a logic 0 and the high tone a logic 1. This is a relatively slow mode of communication, the HAB payload is transmitted at typically 100baud so the 76 byte HAB payload can take 7 or 8 seconds to send.

FSK RTTY is commonly used for tracking high altitude balloon flights in the UK, it can be received over hundreds of kilometres and only requires low cost receiving equipment. An £8 Realtek software defined radio (SDR) connected to a PC is an adequate receiver.

To enable FSK RTTY for the HAB tracker software in the Settings.h file section ‘6) Program Default Option settings’ change the line defining the FSK RTTY option from;

const char option\_FSKRTTYEnable = **OptionOff**; //set to OptionOn to enable transmit of FSKRTTY

to

const char option\_FSKRTTYEnable = **OptionOn**; //set to OptionOn to enable transmit of FSKRTTY

The Settings.h file contains in section ‘5) FSK RTTY Settings’ the various parameters for the FSK RTTY.

### Long Range Search Mode

The LoRa modem settings used the transmit the HAB packet are adequate for in flight use. There is normally good line of sight to a balloon in flight so even modest LoRa settings will achieve long range. However once a tracker has landed, the effective range the LoRa can reach can drop to 1/1000th (or less) of the in flight range. Therefore if you are searching on the ground for your landed tracker, and its still working, the LoRa setting used to send the GPS location might not get very far.

The tracker transmitter software has the option of sending a location only packet at different longer range LoRa settings. This packet only contains the latitude, longitude, altitude and a status byte to keep it short. The difference in receiver sensitivity between the standard settings used for sending the HAB payload (SF8, bandwidth 62500hz, coding rate 4:5) and those used for the search mode (SF12, bandwidth 62500, coding rate 4:5) is 11dBm, which implies the Search mode location packet can be received at 3.5 times the distance over the HAB payload. Note that if the tracker transmitter and receiver are using SX126X LoRa modules the use of much lower bandwidths than 62500hz are possible, which could double search range by a further factor of 2.

The receiver of course needs to know about the LoRa settings, and you can flip the receiver software between normal Tracker mode and Search mode by pressing a switch.

As described in part 1 a portable receiver for ground searching can be very small and light.

### Duty Cycle Issues

Due to legal restrictions for the radio bands typically used by HAB trackers, 434Mhz or 868Mhz, you can be restricted to a duty cycle of 10% if the bandwidth is in excess of 25khz (which it often is for LoRa) so you can only have the transmitter on 10% of the time. For sending the location with only with LoRa using the HAB payload described above this is not usually a problem. There is little to be gained by transmitting the location more often than 20 seconds and you then have about 2 seconds of permitted air time to play with. With the settings used to transmit the HAB payload (SF8, bandwidth 62500hz, coding rate 4:5) the on-air time for a typical 76 byte payload is 484mS.

However there are the other methods of transmitting the location from the HAB tracker which can take longer and you need to consider the duty cycle.

The FSK RTTY transmission is slow at 100baud, sending the 76 byte payload can 9.9 seconds. The bandwidth of this FSK RTTY signal is very small so there are few duty cycle restrictions for this mode. The long range Search packet (bandwidth 62500hz ) takes 2.3 seconds to send so if the tracker is operated in a band where the duty cycle is limited to 10%, care is needed to stay legal.

How often the series of transmissions is sent, which in effect controls the duty cycle, is adjusted by changing the value of the ‘ SleepTimesecs’ constant in the Settings.h file. SleepTimesecs is the time the tracker pauses and does nothing between sets of transmissions.
