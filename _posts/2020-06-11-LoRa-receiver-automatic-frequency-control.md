---
layout: post
title: "LoRa Receiver – Automatic Frequency Control"
date: "2020-06-11"
---



The maximum permitted frequency error between transmitter and receiver, for reception of a LoRa packet, is 25% of the bandwidth in use. 
So at 125000hz bandwidth the maximum frequency error is 31500hz, if the bandwidth is 7800hz the maximum frequency error is 1950hz. 
One of the benefits of the earlier LoRa devices, the SX127X family, was that working products or modules could be made using relatively low cost crystals for the oscillator. 

A typical variation seen in 434Mhz modules from Hope or Dorji for example was that there could be a variation of up to +\\- 4000hz frequency difference when transmitter and receiver modules were at room temperature, so up to 8000hz between transmitter and receiver.

As the bandwidth is lowered, then so the receiver sensitivity increases resulting in longer range capability. In some countries you can use 100% duty cycle below 25000hz channels, so there are benefits in using lower bandwidths.

At 434Mhz and a bandwidth of 20800hz, then the transmitter and receiver must be no more than 5200hz apart which is outside the possible limits mentioned above. You might get two devices communicating ‘out of the box’ when at the same temperature at this low bandwidth but then you might not. If there are also significant temperature differences between devices this can increase the frequency differences too. 

One possible solution is to calibrate each transmitter and receiver such that by using a suitable calibration offset the different devices at least start out at the same frequency. The SX12XX library allows for this in that the frequency of the modules is set with this function;

setRfFrequency(Frequency, Offset);

So you can set modules to the same target frequency and compensate with a calibration value in Offset. There is a program in the SX127x library \examples folder that makes the LoRa device put out a carrier so its easy to measure the actual output frequency. Lets imagine that we want to operate on 434Mhz and a particular module when set for that when measured with a frequency counter is transmitting on 434002000hz, or 2000hz higher than desired. That module would then be setup with;

setRfFrequency(434000000, -2000);

So it now outputs on 434000000hz.

### Automatic Frequency Control

There is another partial solution to dealing with these frequency differences between devices at low bandwidths, you can use a form of automatic frequency correction (AFC). When a packet is received the LoRa receiver will produce an estimate of the frequency difference between the transmitted packet and the frequency the receiver is operating on. This frequency error is stored in 3 device registers and we can read it out. You can then use the frequency error to adjust the frequency of the receiver. 

You might have an initial frequency error of 2000hz and AFC can reduce it to 100hz or so. However whilst the AFC functionality can keep transmitter and receiver close together when reception is working, if the transmitter and receiver are to far apart in frequency for reception to work in the first place then AFC cannot operate to correct for the frequency differences. Some form of manual intervention is needed to allow reception to start, possibly by initiating communication initially at a higher bandwidth. 

The AFC functionality has been added to the SX127xx library, just call the function doAFC(); after a valid packet reception. 

### Errors with frequency error value

The AFC is not without issues however. It was noticed in testing the AFC (see example program 73_LoRa_Receiver_AFC) that occasionally the frequency error would jump by 1000hz or so, I was testing at bandwidth 125000hz SF7. Below is a copy of the test program printout that shows the problem;

    351s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    352s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    353s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    354s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    355s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    356s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    357s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    358s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    359s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    360s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    361s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    362s  Hello World 1234567890*,0,5E,C0,Regval,5EC0,FreqErrror,3179hz
    363s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    365s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    366s  Hello World 1234567890*,0,5E,C0,Regval,5EC0,FreqErrror,3179hz
    367s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    368s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    369s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    370s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    371s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    372s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    373s  Hello World 1234567890*,0,3E,C0,Regval,3EC0,FreqErrror,2105hz
    374s  Hello World 1234567890*,0,5E,C0,Regval,5EC0,FreqErrror,3179hz

The printout shows the time on the left then the packet contents received. Then the 3 individual frequency error registers, REG\_FEIMSB, REG\_FEIMID and REG\_FEILSB. Then the frequency error registers as a combined HEX number and finally the calculated frequency error in hertz. You will note that the frequency error seems very stable at 2105hz with the frequency error registers at 0x3EC0. 

However, note that bit 2 of REG\_FEIMID is randomly set to logic 1, so the HEX registry value changes to 0x5EC0.  This randomness of bit 2 of REG\_FEIMID is not caused by the transmitter shifting in frequency, two receivers were operated receiving the same packets and they both exhibited the same issue, but not for the same packet or at the same time. 

It does not appear to be a hardware issue reading the SPI bus, note that the received packet is always displayed without error. Also there are 3 separate reads of the frequency error register contents, first to display 0,5E,C0, then to display Regval, 5EC0 and finally to display FreqErrror,3179hz. All 3 reads return the same value for REG\_FEIMID.
The same issue was seen on ATmega328P and ATSAM 3X8E (Arduino DUE), 3 seperate SX1278 modules were tried. 

Not sure if there is a fix for this.

