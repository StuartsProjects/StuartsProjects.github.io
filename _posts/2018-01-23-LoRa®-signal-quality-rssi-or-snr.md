---
layout: post
title: "LoRa® Signal Quality – RSSI or SNR?"
date: "2018-01-23"
---

It's helpful to know how good the LoRa® signals you are receiving are, or perhaps more importantly how close the are to failure.

The LoRa® device measures two things during packet reception, the received signal strength indicator (RSSI) measured in dBm, and the signal to noise ratio (SNR) measured in dB.

**SNR**

From practical experiments I have observed that as the reported SNR approaches the limit specified for the spreading factor (SF8) then the packet reception will start to fail.

For instance at SF8 the SNR limit is -10dB. If your transmitting packets and they are being received at SNR of -0dB and you then reduce transmit power by 8 or 9dBm, the reported SNR will drop to around -9dB and packet reception starts to fail. I have found the reported SNR a very good indication of approaching reception failure.

**RSSI**

Under very good reception conditions, with strong signals, the reported SNR rarely goes above 8dB, even as signals get stronger. So for very strong signals SNR is not a good indicator of signal quality.

I decided to plot the results of a large variety of reception conditions, at SF7 bandwidth 125000hz. The signals varied from very strong, the transmitter and receiver close together, to very weak signals where packet reception was failing. I plotted the results, see the graph below.

![SNR versus RSSI Graph](/images/SNR-versus-RSSI-Graph_thumb.jpg "SNR versus RSSI Graph")

You can see the spread of reported RSSI for SNRs of 10,9 and 8, the range is -45dBm to -105dBm. So clearly SNR is not a lot of help for giving a quality indication for very strong signals.

But then look what happens as the SNR drops, at the failure point, -8dB SNR and below, the RSSI varies from -100dBm to -120dBm.

For an overall quality indicator, perhaps a compromise is needed. If the SNR is say 7dB or higher, use the RSSI figure, and if SNR is lower than 7dB, use the SNR.

Just a thought.
