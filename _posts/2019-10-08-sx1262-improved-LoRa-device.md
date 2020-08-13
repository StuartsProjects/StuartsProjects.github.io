---
layout: post
title: "SX1262 – Improved LoRa Device"
date: "2019-10-08"
---

NiceRF recently released a module using the Semtech SX1262 LoRa module. This is for UHF use and seen as an upgrade to the SX127x devices. I designed a breadboard friendly module for it so I could test the device.

![](/images/word-image.jpeg)

**_Edit: 08/08/2020_**

I have published an Arduino library for the SX12XX devices which includes the SX1261, SX1262 and SX1268. It can be found here; **[SX12xx Library](https://github.com/StuartsProjects/SX12XX-LoRa)**. 

Standard LoRa modules such as Hope RFM98 and Dorji DRF1278 based on the Semtech SX127x RF chip generally use low cost crystals which whilst it makes the modules cheaper, using LoRa bandwidths below 62500Hz is difficult. If the transmitter and receiver differ in transmission frequency by more than 25% of the bandwidth in use, they wont communicate. At these lower bandwidths even if two devices communicate on the bench when they are at the same temperature, large temperature differences can cause communications to fail.

This frequency error problem is going to be worse at 868MHz than it is at 434MHz, so its no surprise that The Things Network uses a bandwidth of 125000Hz, it leads to reliable communications with low cost devices..

The Semtech SX1262 has internal firmware support for a temperature compensated crystal oscillator which should allow the lower bandwidths to be used.

Lower bandwidths potentially provide longer range, at the expense of packets taking longer to send. Significantly in a lot of countries the duty cycle for less that 25kHz channels is often 100% , but the higher bandwidths commonly used by current LoRa modules are normally restricted to 10% or even 1% duty cycle. So the lower bandwidths can provide a greater data throughput since you can run the transmissions continuously.

It is possible to get the standard SX127x modules to communicate at the lower bandwidths. Once packets are being received the transmitter and receiver can be kept close in frequency (mitigating developing temperature differences) by using the estimated frequency error produced by the SX127x to adjust the set frequency of the receiver. However packet communications does need to be established in the first place either by a manual adjustment of frequency or by initially receiving packets at a higher bandwidth.

The NiceRF SX1262 modules I had worked out of the box at the lowest possible bandwidth of 7810Hz, which was a surprise, practical operation at that low bandwidth was just to difficult with the previous generation SX127x modules.

So how would the modules cope with large differences in temperature? A transmitter was setup for bandwidth 7810Hz at spreading factor 7 (SF7) using 10dBm of power. The transmitter was put in a freezer that goes down to -20C. After a couple of hours in the freezer I was still receiving packets on the bench receiver at +20c, so the use of the TCXO allows the lowest bandwidth of 7810Hz to be used even with large variations in temperature, 40c in this case, between transmitter and receiver.

So what is the amount of frequency shift of the SX1262 modules when the temperature goes from +20c to -20c? I theorised you could identify the lower and higher limits of working reception by adjusting the set frequency of the receiver till reception of packets stopped.

I was using a frequency of 434.5MHz. So by adjusting the frequency of the receiver I determined the low frequency for working receptions was 434,498,250Hz and the upper frequency was 434,502,050. This is a range of 3800Hz or +/- 24%, which is closely in line with the data sheet specification of 25%. Centre frequency was 434,500,150Hz. This was with the receiver at +20c and the transmitter at -20c.

The transmitter was allowed to return to room temperature, +20c, and the frequency limit test carried out again. This time the lower frequency of reception was 434,497,950Hz and the upper frequency was 434,501,700Hz. This is a range of 3800Hz or +/- 24%. Centre frequency was 434,499,850Hz.

Thus the shift in frequency of the transmitter in the temperature range of +20c to -20c was 300Hz or 7.5Hz per c. So if there was a difference in temperature of say +30C to -60c between transmitter and receiver the frequency difference between them due to temperature would be 675Hz.

It therefore seems feasible to use a bandwidth of 20830Hz without worrying about frequency errors and over a wide temperature range.

At 434MHz, a bandwidth of 62500Hz SF12 would normally be used for a location tracker and changing to using a lower bandwidth of 20830Hz should, according to the data sheet, provide an additional gain of 4.8dB, equivalent to increasing the reception distance by 74%. Moving bandwidth from 62500Hz SF12 to 20830Hz SF12 would increase on air time of a 12 byte location packet from 1982mS to 5948mS.

So how well does the SX1262 actually perform at the 20830Hz bandwidth, and how to carry out a real world test?

I have some link test software that will send packets at reducing powers, the SX1262 has a range of 22dBm to -9dBm. Imagine you want to compare the effectiveness of two antennas. You will first need to attenuate the transmitter output in some way to cut the range to a practical value, you can use an SMA attenuator (20dB possibly) between the transmitters LoRa module and antenna or attenuate transmissions in some other way by putting the transmitter in a tin or wrapping it in aluminium foil.

At some distance from the transmitter you attach antenna A to the receiver and you note it stops receiving packets sent at -2dBm, no lower power packets are received. The power level a packet is sent at is made part of the payload so the receiver knows the powers of the packets received.

Keeping the receiver in the same position and orientation you fit it with antenna B. This time you receive all packets down to -5dBm. Thus the **real World performance** of antenna B is 3dBm higher (i.e. it has 3dB more gain) than antenna A.

You can use the same testing method to compare the effectiveness between two sets of LoRa modem configurations. I set my link test software to send descending power packets

first at 62500Hz SF12 and then at 20830Hz SF12. The receiver software flips reception between the two settings automatically and displays the results on a display and logs to micro SD card for later review. The transmitter was placed on top of the mast on my shed and I took the portable receiver to a hillside overlooking the city where I live and 4.4km away.

![](/images/word-image-1-1024x829.jpeg)

The results were interesting, over about 10 test loops the average of results were;

**62500Hz SF12 reception down to 0dBm**

**20830Hz SF12 reception down to -6dBm**

So there was a practical difference. I did notice that there were more packet failures at 20830Hz SF12. At the limit of reception these time wise longer packets are probably more susceptible to corruption from noise etc.

Looking at a comparison of bandwidth 62500Hz SF7, which is a data rate of 2735bps at 10% duty cycle you can only send at an effective 273bps. At 62500Hz 20830Hz SF7, the data rate is lower at 910bps, but since you can send at 100% duty cycle you have over 3 times the data throughput. You also pick up around 5dB of signal gain. Winner all round really.

In summary the SX1262 is a very useful addition to the LoRa family, it can be used for longer range applications due to the TCXO making lower bandwidths practical. The other probable application is using the 20830Hz or lower bandwidth to send data on a continuous basis and avoid duty cycle restrictions.

To carry out the above tests I needed to build a suitable Arduino library and whilst the methods of controlling the SX1262 are completely different to the SX127x devices, the SX1262 is very similar in operation to the SX1280 for which I had already produced a suitable Arduino library. There are only some slight differences in commands so my testing programs originally written for the SX1280 worked with only minor changes on the SX1262.

On point to note; the SX1262 now goes down to SF5 and both SF6 and SF5 now support the use of explicit and implicit header types. However the changes made to produce these new features mean that the SF6 output produced by an SX1262 cannot be read by the earlier SX127x devices. Which is a big shame.

Perhaps of particular interest to those using TTN applications is the almost identical SX1261 device which apparently (according to the data sheet) uses the switched DC-DC converter to significantly reduce power consumption dureing transmissions. This if it proves to be true could yield a significant improvement in battery life for projects so powered.

**Stuart Robinson**

**August 2019**
