---
layout: post
title: "A LoRa mystery – Phantom packets but deeper"
date: "2018-05-31"
---

Despite the shielding of my LoRa receivers being able to resist 50mW at 5cm it was possible that there could be a Megawatt LoRa transmitter somewhere close that was the cause of the phantom LoRa packets I was seeing at 868Mhz bandwidth 500khz and spreading factor 7. It’s unlikely there would be such a powerful transmitter nearby that I did not know about, but it seemed a good idea to eliminate it as a possibility.

What I needed was a test area that was in a relatively remote, was underground and with no openings to the outside world. Fortunately up in the hills nearby is a disused railway tunnel, it's at a height of 439m AGL, the tunnel is 600m long and curved so that in the middle there is no line of sight to the outside world. The tunnel is some 40m underground in the middle. There is a quiet road nearby with a few cars, the nearest building is 1km away. There are a few further buildings 3km away.

I went to the tunnel on a wet afternoon with my brother (Neil) to see if there would be any difference in the number of phantom packets received and their signal strengths in the middle of this tunnel. I used two receivers heavily screened as before and took the precaution of eliminating all possible EMI producing gadgets, phones off, cameras off, I even took the battery out of my car key fob.

![](/images/Tunnel-300x225.jpg)

With the two receivers on the floor I could still hear the beeps indicating phantom packets at the same rate as I had been getting in my workshop and local field.  Looking at the logs the receivers produced later it was clear that the signal strengths in the workshop were the same as I was seeing deep underground and a very long way from any LoRa transmitters.

![](/images/RX-in-Tunnel-300x225.jpg)

 

**Log**

_ReqIrqFlags 0x50 CRC and Header OK SNR,-11dB,RSSI,-114dBm,Length,153 Packet data 9E,62,C8,7C,55,B2,6F,E1,40,8A,F8,D4,B1,99,0C,33,EA,E1,58, (packet data truncated) Valid Packets 7  CRC Errors 2  Header Errors 0_

Reported signal strength in all the tests, with an antenna on a tall mast, with small antenna on the bench, with an SMA terminator on the antenna socket on the bench, heavily screened on the bench and now with a heavily screened receiver deep underground were always the same, –112dBm to –114dBm and with SNRs of –11dB or –12dB. My assuimption would be that the source of the phantom packets was the receiver itself.

With  the microprocessor in use (ATMEGA328) being put to sleep this suggests the source of the phantom LoRa packets may be the LoRa device. If that is the case how can it be that the CRC is valid for about 70% of the phantom packets? My logging software was printing out the contents of the ReqIrqFlags register and that indicates a valid header (which has its own CRC) as well. 

What are the chances of the CRCs for header and payload being valid in a packet that's some sort of random occurrence of noise?
