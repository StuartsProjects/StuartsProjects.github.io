---
layout: post
title: "Frequency drift for LoRa devices, is it important?"
date: "2020-05-08"
---

If LoRa devices are operated within the limits specified for frequency variations between LoRa transmitters (TX) and receivers (RX) then LoRa communication will work reliably. However if the operating frequency of a LoRa TX and RX are too far apart in frequency, then communications will fail.

The Semtech data sheets state that the frequency error allowable between TX and RX is 25% of the bandwidth in use. If the bandwidth in use is 62500hz and the TX is on 434Mhz, then for communications to work the RX must therefore be within +\\- 15625hz or between 433.984375Mhz and 434.015625Mhz in this case.

In practice this 25% of bandwidth frequency tolerance is not usually a problem. Even with the low cost crystals used in a lot of the SX127X LoRa modules for 434Mhz operation the typical static frequency variation is +\\-4000hz between different modules at the same temperature. What will happen if say the RX device is at room temperature and the transmitter is at -40c? This can happen for instance when LoRa devices are used as trackers in high altitude balloon flights.

A bandwidth of 62500hz at 434Mhz and 125000hz at 868Mhz are in general the lowest bandwidths used where there needs to be a reasonable expectation that frequency errors between LoRa devices caused by either by crystal manufacturing tolerances or temperature variations will still allow LoRa devices to talk to each other.

### Low LoRa bandwidths

If you were to use the minimum LoRa bandwidth allowed, 7800hz, you would be able to gain a link advantage of around 9dBm over using a bandwidth of 62500hz and distances achievable should increase by a factor of 3. However at this bandwidth the TX and RX now need to be within 1950hz of each other which is impractical for SX127x devices that use the low cost crystals. The narrow bandwidths can be made to work if the frequency of TX or RX is manually adjusted, but you can only do this if you know for sure what the TX or RX frequency actually is. The LoRa device can report on the frequency error between TX and RX but to report the difference you do need communications to work in the first place.

### FSK RTTY - Frequency Shift Keyed Radio Teletype

One non-LoRa use of the SX12XX devices is the generation of FSK RTTY which is commonly used for tracking high altitude balloon flights in the UK. FSK RTTY can be received over hundreds of kilometres whilst only requiring low cost receiving equipment. An £8 Realtek software defined radio (SDR) is adequate. One problem with FSK RTTY is that frequency drift of the transmitter requires that the base frequency of the receiver needs to be adjusted to produce the appropriate audio tones. The decoding software software commonly used, dl-fldigi, does have an auto tuning function, but that only works over a couple of kHz in the narrow audio band. In practice whilst FSK RTTY is long distance it can require a fair bit of manual effort to keep the receiver on tune.

LoRa devices will drift in frequency due to two main factors, first the shift caused by normal changes in air temperature for instance as a balloon rises and falls, plus the thermal heating of the LoRa devices RF chip when transmit is turned on and heats the device up.

### Use of Temperature Compensated Crystal Oscillators (TCXOs)

The newer SX126X LoRa devices have built in support for a temperature compensated crystal oscillator (TCXO) and these are fitted to most of the available modules. The TCXO is used for the frequency reference and is little affected by heating from the LoRa transmitter and far less susceptible to external temperature changes over standard crystals.

What would the differences in frequency shift be in practice between a standard SX127X device and a TCXO equipped SX1262?

I setup a HAB tracker based on a Arduino Pro Mini and using HAB transmitter and receiver software for the SX12xx library. The receiver application reports the frequency error between transmitter and receiver. For the test I had the receiver at a stable room temperature (20c) whereas the transmitter was first on a table in the garden at a temperature of circa 28c and then moved into my freezer for a while where the temperature dropped to circa -20c. After a period the tracker was put out in the warm again.

The frequency errors reported by the receivers were recorded and graphed against time.

The SX1276 device went up and down in frequency (blue line on graph below) quite a bit as the temperature varied between about +28c to -20c to 28c again (yellow line). The variation over that temperature range was about 2200hz, certainly enough to cause problems with FSK RTTY.

![](/images/SX1276.jpg)

The SX1262 with its TCXO was a lot better with about 200hz frequency drift over the same temperature range. Its possible that using the SX1262 as a HAB tracker would mean the FSK RTTY does not need tuning intervention, assuming the receiver was at a stable temperature. Also the frequency shift of 200hz of the SX1262 is well within the 1950hz frequency variation mandated for operation at the longest range settings of 7800hz bandwidth.

![](/images/SX1262-1.jpg)

Perhaps a HAB flight test is in order once a sort of normality is returned to for what purposes we can venture outside.

