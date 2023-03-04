---
layout: post
title: "FSK RTTY versus LoRa®"
date: "2015-11-01"
---

## How effective is FSK RTTY versus LoRa®, will one go further than the other ?

<br>

For a Pico high altitude balloon flight in October 2015, I integrated some new test software into the tracker code. The purpose of the test software was to enable a comparison to be made between the effectiveness of LoRa data telemetry and FSK RTTY as produced by a LoRa device, RFM98. 

By remote command from the ground, the in flight tracker would send a series of LoRa packets descending in power from 10dBm to 2dBm, with the power level being part of the packets. The same was done for FSK RTTY a short series of text packets were sent at descending powers. If the LoRa was received at say 5dBm but the FSK RTTY at 2dBm, you could assume the FSK RTTY had a link margin benefit of 3dBm and would also have further range.


The test software gave an indication of the performance differences but it was too crude, what was needed was a method of adjusting the LoRa parameters so that the LoRa device had the same reception at a particular power level as was the case for the FSK RTTY. 

It took a while to get working properly, but eventually I setup remote control for the LoRa tracker which allowed me to select on the LoRa receiver the parameters (bandwidth, spreading factor and coding rate) from a menu and send these test parameters to a remote tracker, the tracker would then respond with a series of packets at the selected LoRa parameters and with a power level that reduced by 1dBm for each packet.

By flipping the receiver to the same set of LoRa parameters you could note the power level at which packets stopped being received. The receiver was also able to tell the transmitter to send a set of FSK RTTY packets at descending powers.

The transmitter and receiver needed to have the antennas significantly attenuated so that testing could take place in a large open space, a local field. The testing was not designed to quantify the actual link margin performance but to compare the relative effectiveness of various modes. 

For the FSK test I used an Yaesu FT817 as the receiver, hardly a slouch at UHF. A laptop PC and FLDIGI were used to decode the RTTY audio. The LoRa receiver was an RFM98 LoRa device, the entire receiver costing around £10 to build. The receiving antenna was remote from where the receivers were placed and connected by co-ax, so that the same antenna could be swapped between the FT817 and LoRa device.

At a typical 100baud the FSK RTTY stopped being received error free at 13/12dBm. 

With LoRa set at 98baud (BW41.7, SF12, CR4:5) it was received down to 6/5dBm, so a 7dBm advantage.

By adjusting the LoRa parameters to match the fail level (in dBm) of the FSK RTTY, the LoRa data rate to match the FSK RTTY fail rate was found to be 788bps.

The consequences of this is that in distance terms 788bps LoRa has the same range as the 100baud FSK RTTY, but the RTTY uses around 8 times the battery power to send a position payload. In this case using LoRa instead of FSK RTTY could significantly extend battery life. 

Or you could reduce the LoRa rate down to as low as 100bps, so the battery power consumption is the same, but have the benefit of around twice the range.

#### Stuart Robinson
#### GW7HPW
