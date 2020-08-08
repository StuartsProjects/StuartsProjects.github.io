---
layout: post
title: "A LoRa Mystery – Phantom Packets."
date: "2018-05-29"
---

I had noticed  while testing LoRa links at 868Mhz with different receiver types that I was seeing a number of ‘phantom’ packets that I did not recognise. I initally thought these were just LoRa packets from the surrounding environment. 

The receiver I was using was listening on Bandwidth 500Khz and spreading factor 7, which is not the typical packet settings used by LoRa for Internet of things applications. I was using these settings as I needed a fast data rate and only a hundread meters or so of range. These phantom packets being quite short range I thought must be coming from somewhere nearby.

I used some LoRa SD card logging software I had written to check out the details of these phantom packets. I could leave the receiver running for long periods to see how many packets were received. I did fing a bug in the software which explained some odd RSSI and SNR values I had been seeing but I was still curious as to where the packets were coming from.

 ![](/images/LoRa-Receiver.jpg)

With the receiver running on the bench with an antenna I was getting around 5 to 10 phantom packets per hour, these were a mixture of packets with valid CRC and those where the CRC check had failed. Packets varied in length from a few bytes up to 200+. The reported RSSI and SNR was always around –110dBm and –10dB respectively which suggested a single source of the packets. 

I put an SMA terminator in place of the antenna and was surprised to see that I got around the same results, 5 to 10 packets per hour and similar RSSI and SNR values.

As a check to see if these phantom packets were actually coming from the great wide world I put my reference 868Mhz antenna (1/4 wave vertical with radials) on top of the 8.5m mast attached to my workshop. I got the same results as I had when the receiver was sat on the bench with an SMA terminator on it. The assumption might be that the source of the phantom packets was therefore very local, interference from my PC or the lights perhaps ? That however would not explain why I origionally seen these packets in the middle of a large field.

For some of the LoRa link testing I had been doing recently I needed to cut the range of the LoRa transmitter (at spreading factor 12) to just 50m or so. Fitting an SMA terminator in place of the antenna was not enough. Even putting the receiver in a die-cast aluminium box was not enough. What did work very well was wrapping the transmitter in aluminium foil. That cut the reception range to 10m or so and it was easy to adjust the range out to 50m by using a pin to put small holes in the aluminium foil.

![](/images/LoRa-Test-Transmitter.jpg)

![](/images/Screened-Transmitter.jpg) 

If the aluminium foil was so effective at preventing the RF getting out, it ought to be effective at preventing RF getting in. So I wrapped a LoRa receiver in foil and also put it in an   aluminium box. A LoRa transmitter sending packets at 17dBm (50mW) had to be within 5cm of the box for the receiver to pick up the packets, there was a buzzer on the receiver so I could tell when packets were received. So if real world packets were getting through all that shielding they had to be very powerful indeed.

![](/images/Screened-Receiver.jpg)

With the receiver now wrapped in foil and consequently very well screened I was still receiving phantom packets and with the similar RSSI and SNR values as before. Perhaps the phantom packets were a result of electro magnetic interference (EMI) coming from the receiver itself, but if so how could the CRCs be valid ?

I modified my logger software to put the Arduino Pro Mini to sleep and have it wake up when a packet was received using an interrupt from the LoRa devices DIO0 pin. I even powered down the SD card. Thus the only active electronics running (and thus possibly generating EMI) was the LoRa device itself listening for a packet.

With only the LoRa device itself active and with it heavily screened from the outside world, still the phantom packets appeared.

These phantom packets do seem to be coming from the receiver itself; when I ran two identical receivers next to each other on the bench, each would receive phantom packets but not at the same time. If the source of the packets was external then you might expect at least some duplicate receptions.

This does not appear to be just an issue with the 500khz bandwidth setting, IO have also seen phantom packts when the 125khz bandwidth setting is used.

Maybe the LoRa receiver is fooled into starting packet reception by noise but then why does reception complete with a valid payload CRC and a valid header CRC as well?
