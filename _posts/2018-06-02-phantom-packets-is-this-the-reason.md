---
layout: post
title: "Phantom Packets – Is this the reason ?"
date: "2018-06-02"
---

After consulting Semtech, the reason why these phantom packets are seen becomes clear.

This is my current understanding of the issue but testing is continuing …………….

The LoRa® receiver will occasionally falsely see noise from the receiver itself as a packet preamble and header. Since its noise from the receiver this explains why the phantom packets are seen even in heavily screened receivers underground.

The header is protected with only a 6 bit CRC so there is a 1:64 chance that this noise will pass a valid header check.

When sending LoRa® packets you have the option of using a payload CRC (16 bit) or not. If there is a payload CRC enabled a flag bit is set in the header. When receiving the packet the LoRa® receiver tests for this payload CRC enabled flag and carries out the CRC check on the payload.

The trap you can fall into is in assuming that since your application only sends packets with CRC on the payload enabled, on packet receipt you only need to check the RegIrqFlags register PayloadCrcError bit. If its set there is a CRC error, if it clear then you assume your packet is valid. But it may not be.

When one of the phantom packets is received (which are just noise remember) there is a probability that the false header both passes the header CRC check and does not have the payload CRC enabled bit set. If this bit is not set in the header then the LoRa® receiver does not carry out a payload CRC check and the CRC error flag is left clear.

There are some LoRa® software libraries that on packet receipt appear to check the header for the payload CRC enabled flag and then check the payload PayloadCrcError flag as appropriate. This makes sense if your receiver software is designed to automatically cope with packets that use a payload CRC and those that do not.

So in summary to ensure that you do not read phantom packets as valid, you should consider;

1\. Always sending packets with the payload CRC enabled .

2\. Always rejecting packets when the the RegIrqFlags register has the PayloadCrcError bit set. 

3\. On packet receipt check the RegHopChannel register RxPayloadCrcOn bit, if this bit is clear (meaning the packet has no CRC) dump the packet, it could be a phantom.

