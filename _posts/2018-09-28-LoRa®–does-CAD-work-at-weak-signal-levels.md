---
layout: post
title: "LoRa® – does CAD work at weak signal levels"
---

The SX127x data sheet defines CAD as;

***When in CAD mode, the device will check a given channel to detect LoRa® preamble signal***

And;


***Principle of Operation
The channel activity detection mode is designed to detect a LoRa® preamble on the radio channel with the best possible power efficiency. Once in CAD mode, the SX1276/77/78/79 will perform a very quick scan of the band to detect a LoRa® packet preamble.***

<br>

There is little to suggest that CAD works on the body of the packet, which in practice is the major par of the transmit time. So whilst CAD is supposed to detect the pre-amble of a packet, does it detect a packet at other times ?

To test this the idea would be to transmit long packets (of several seconds) and have a receiver constantly running the CAD process. When each CAD period was finished the CAD detected flag would be read and a LED turned on or logic level set if the flag was active. Thus by observing the LED so that you could see for how long a packet was detected by CAD and how consistent the detect was.

The packets would be sent in sequence of descending powers (17dBm down to 2dBm) so it would be possible to see by observing the LED how well CAD performs as the signals got weaker. The link test software I use was changed to send packets that were close to 5 seconds long at SF12 and bandwidth 125khz.

The test transmitter was fitted with an SMA terminator on its antenna input, this should cut the actual transmitter power (and received distance) significantly.

I did an initial test with transmitter and receiver close. It’s clear that the CAD worked on the entire packet. In my test the LED on the transmitter (indicating a packet send in progress) closely followed the CAD detect on the receiver. There was one curious exception and it was consistent, approximately 360mS into the 5079mS packet there would be one CAD detect that failed. It was also noted later that this gap occurred with weak signals too.

With the principle of CAD working on the entire packet established I moved on to seeing if it also worked on weak packets.

The transmitter and receiver were loaded with my link test software and I adjusted the locations of both so that the packets stopped being received at around the 9dBm point.

Here is the link test software output;

Mode1 17dBm,11 16dBm,11 15dBm,11 14dBm,11 13dBm,10 12dBm,10 11dBm,11 10dBm,10 9dBm,8 8dBm,4 7dBm,5 6dBm,0 5dBm,1 4dBm,0 3dBm,0 2dBm,0

Mode1,CSV,11,11,11,11,10,10,11,10,8,4,5,0,1,0,0,0

You can see from the counts that packet reception starts to fail at around the 9dBm point and stops almost completely after 7dBm.

Once the test link had been characterised the receiver was loaded with the CAD detect test software. The positions of the transmitter and receiver were kept the same to ensure consistent link performance.

With the receiver LED being on when CAD succeeds, we can easily tell when a packet is being received. At the start of the descending power sequence (17dBm packet) there is an audio tone transmitted which can be heard on a UHF hand-held. When the tone from the remote transmitter is heard you can now count the arriving packets by observing the CAD detect LED. By counting the packets you know which power is in use when the CAD starts to fail, indicated by the LED flickering.

The CAD process was consistent (apart form the gap @ 360mS mentioned above) and started breaking up at around 14 to 13dBm dBm so within about 4 or 5dBm of link failure, this is better performance than expected.

So in approximate terms CAD detect does appear to work on the entire packet, but is only consistent up to around half the distance that the weakest packets are detected.

In the CAD detect software used each CAD detect and print of information to console was taking approximately 62mS. I left the receiver running for 10 minutes to see if there were any false CADs. There were 119 false CADs in 10 minutes or about 1 every 5 seconds or about 1 : 80 CADs are false.

What of the gap in the CAD at the beginning of the packet?  This would appear to be at the time the preamble pattern ends and the packet data starts, so it's possible CAD will see the preamble, or the packet, but not when both are mixed together.