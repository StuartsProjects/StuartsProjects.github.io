---
layout: post
title: "How to use low bandwidths with the Tracker programs."
date: "2018-01-24"
---

These comments relate specifically to my own tracker programs which can be found here;

[https://github.com/StuartsProjects](https://github.com/StuartsProjects)

Using a low LoRa® bandwidth can improve the range\\distance you can achieve. You can do much the same by increasing spreading factor. Range improvements have a cost, the packets take longer to transmit. At bandwidths above 25kHz local regulations may restrict you to 10% duty cycle, so a long range low data rate packet that takes maybe 5 seconds to send can only be sent every 50 seconds or so.

With a low LoRa® bandwidth packet you can likely use greater duty cycles so you may be able to send the packet continuously, although do check your local regulations.

However, at the lowest possible LoRa® bandwidth, 7.8kHz, the LoRa® transmitter and receiver are very likely to be too far apart in frequency for packet reception to initially work and some combinations of modules may not work together even at 41.7khz. Once we have packet reception actually taking place at a low bandwidth we can use automatic frequency control (AFC) functionality to keep the receiver within a few hundred hertz of the transmitter, even if the transmitter frequency changes due to temperature shifts etc.

The Tracker receiver programs can deal with this issue, more or less automatically, there is no need for manual adjustments of receiver or remote adjustments of the transmitter.

The HAB2 Tracker program has a bind functionality, the transmitter sends a bind packet at start-up. The receiver can be put into bind mode and will pick up and use the LoRa® settings of the transmitter. A consequence of this bind is that the receiver will also adjust to the frequency of the transmitter. However the bind functionality is kept short range on purpose, so if your receiver is some distance away, or is started up after the transmitter powers up, you wont be able to use the bind to frequency lock your transmitter and receiver.

By default the HAB2 tracker program puts out a binary location only packet (the search mode packet) at bandwidth 62.5kHz spreading factor 12 (SF12). This packet is only 12 bytes long so can be sent fairly often even at this 'high' bandwidth. Lets imagine the tracker mode packet (HAB payload) has been set to use a bandwidth of 7.8kHz SF8. If the transmitter and receiver are approximately 2kHz apart in frequency packet reception will not work.

The 62.5kHz bandwidth search mode packet has a much greater capture range of 15khz, so its unlikely transmitter and receiver will be far enough apart to stop packet reception working.

All we need to do is temporarily switch the receiver over to search mode, wait for it to receive a packet, then swap back to tracker mode. The reception of the search mode will change the receiver frequency to match the transmitter and the two should then stay locked together.

If your not using the search mode packet you could switch the receiver across to command mode listen and achieve the same automatic set-up.

The changes to the LoRa® tracker programs to allow this to work are in this program;

“Basic\_Receiver\_Terminal\_Beta\_240118”

To be found here;

[https://github.com/StuartsProjects/Receiver1](https://github.com/StuartsProjects/Receiver1)

You will also need the updated Tracker library from here;

[https://github.com/StuartsProjects/Tracker-Library](https://github.com/StuartsProjects/Tracker-Library)

It would be straight forward to automate the use of the low bandwidths, just send out a regular very short higher bandwidth SF12 packet to act as an AFC calibrator. The receiver initially listens for this packet, does the AFC when its received and switches across to the low bandwidth mode.
