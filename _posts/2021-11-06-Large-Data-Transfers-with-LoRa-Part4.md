---
layout: post
title: "Large Data Transfers with LoRa - Part 4 - 2.4Ghz SX128X"
date: "2021-11-06"
---

## Data Transfer Library for SX128X

I had initially written the first data transfer library code for the SX127X and started to move it across on to support the 2.4Ghz SX128X devices. There are few duty cycle restrictions for the 2.4Ghz band which makes this band a much better choice for large data transfers versus the duty cycle limited UHF bands.

The SX128X has both LoRa capability, at up to 203kbps on-air rate and there is also the Fast Long Range Communication (FLRC) mode of the SX128X devices that has an on-air rate of up to 1300kbps. These are considerably faster than the maximum air rate of 37kbps for the UHF SX127x devices. The FLRC modes have a slightly higher receiver sensitivity over the LoRa setting for similar data rates, so should cover greater distances.

Some modifications to the current SX128X library were needed to allow the FLRC mode to be used with most all of the transmit and receive functions. There are also some issues with FLRC in dealing with packet errors that needed to be included to allow the FLRC to be used for Data Transfers.   

The SX128X library first needed to implement the Reliable functions from the SX127X library so that the basis of the use of network IDs and payload CRCs could be thoroughly tested. Once this was done I could be sure that the basics of reliable send and acknowledge for packets that would be needed for the data transfers would work.  

## Data Transfers on the SX128X

The basic idea of how the transfers would work has been discussed in previous articles. A file on an SD card or a memory array, is transferred in a sequence of numbered segments. The transmitter first sends a command to the receiver to open a file on it's SD card to receive the transfer. The transmitter then sends the sequence of segments, waiting for an acknowledgement from the receiver before moving onto the next segment. When all segments are sent the transmitter sends a close file request. All segment transfers are protected by payload CRCs and network IDs appended automatically to the end of the packets. 

If the receiver detects that it has received a segment out of sequence, it informs the transmitter, which should revert back to sending the segment the receiver was expecting. Packets which fails the payload CRC or network ID check are rejected and no acknowledge is sent. 

Since it is possible that the transmitter does not receive the acknowledge from the receiver, the receiver does also need to deal with the receiving of the same segment that it has already been written to SD. If this out of sequence situation occurs, the receiver sends an acknowledge back to the transmitter requesting the next expected segment. 

One all segments have been transmitted a close file request is sent by the transmitter.  
 
## Achieved data rates with SX128x - 2.4Ghz

With the Data Transfer working I was able to transfer an image from one Arduino to another using the 203kbps LoRa mode of the SX1280. An image file was used, since its easy to see if there are any errors in the transfer by looking at the received image on a PC, just put the receivers SD card in a USB adapter.  

The image file was 63091 bytes and it had taken a UHF SX1278 29.98 seconds to transfer this image file from the SD card on one Arduino DUE to the SD card on another DUE.  Using 2.4Ghz SX1280 devices the file transfer completed in 4.54 seconds, which is an achieved data rate of 111,296bps and 6 times faster than the SX1278 could manage. 

Originally the file transfer had taken around 8 seconds on the SX1280 but an improvement in the code of the SX128X checkbusy() function speeded up the process quite a bit. With most previous applications for the SX128X library code the potential for improvement in that routine had not been noticed. 

## Long distance Data Transfer simulation

During a high altitude balloon flight, see here;

[**https://stuartsprojects.github.io/2019/10/07/2-4ghz-nicerf-sx1280-lora-balloon-tracker-85km-achieved.html**](https://stuartsprojects.github.io/2019/10/07/2-4ghz-nicerf-sx1280-lora-balloon-tracker-85km-achieved.html "85km Achieved")

I had used SX1280 LoRa settings of Spreading factor 10, bandwidth 400khz for the ranging (distance measuring) capability of the SX1280. The ranging process involves a packet exchange so its a form of two way communication and it had worked at 85km. With these same LoRa settings how long would it take to transfer this 63091 bytes, appreciating that the effective data rate is only 3170bps.  The transfer took 267 seconds. 

## SX128x - Fast Long Range Communication - FLRC

The SX128X has an FLRC mode that operates differently to LoRa but still makes use of forward error correction as a means of improving reliability at low signal levels. At the higher data rates, LoRa at 203kbps has a datasheet sensitivity of -99dBm, whereas FLRC at 260kbps has a sensitivity of -103dBm which ought to go about 60% further distance wise.   

A sketch written for LoRa is fairly easy to swap across to FLRC, first the typical modem parameters used for LoRa in the above tests;

	const uint8_t Bandwidth = LORA_BW_1600;         //LoRa bandwidth
	const uint8_t SpreadingFactor = LORA_SF5;       //LoRa spreading factor
	const uint8_t CodeRate = LORA_CR_4_5;           //LoRa coding rate

need to be changed to these settings for FLRC;

	const uint8_t BandwidthBitRate = FLRC_BR_1_300_BW_1_2;    //FLRC bandwidth and bit rate, 1.3Mbs               
	const uint8_t CodingRate = FLRC_CR_1_0;                   //FLRC coding rate
	const uint8_t BT = RADIO_MOD_SHAPING_BT_1_0;              //FLRC BT
	const uint32_t Syncword = 0x01234567;                     //FLRC uses syncword

Next the setup() sketch function needs to be changed from;

	LoRa.setupLoRa(Frequency, Offset, SpreadingFactor, Bandwidth, CodeRate);

to;

	LoRa.setupFLRC(Frequency, Offset, BandwidthBitRate, CodingRate, BT, Syncword);

And finally the DTSegmentSize needs to be set to a maximum of 117. Since the maximum packet length for FLRC is 127bytes and there is a 6 byte header and the 4 bytes added to the packet for networkID and payload CRC, 117 bytes is the maximum size of the data payload. 

With the changes for FLRC mode made and the file CRC checking turned off FLRC transmits the file in 2.48 seconds, an achieved data rate of 203,601bps.  

## Transfer rate comparisons

To allow a direct comparison between the various LoRa modules and modes I wrote a simple sketch that took a configurable total transfer size and split it up into a number of segments and timed the transmission of all the segments to calculate an achievable data rate. There was no use of acknowledge or checking on a receiver, I just wanted to know how the various different modules and modes would affect how fast you could transmit a block of data. I was also able to adapt the transfer sketch in the RF24 library and run tests on that module also. These were the results for a 65536 byte transmission;

	SX1280 LoRa at 202,000bps air rate, 3020mS, an achieved data rate of 173,836bps
	SX1280 FLRC at 260,000bps air rate, 2949mS, an achieved data rate of 177,785bps
    SX1280 FLRC at 1,300,000bps air rate, 780mS, an achieved data rate of 673,027bps
	SX1262 LoRa at 59,000bps air rate, 9660mS, an achieved data rate of 54,290bps
	SX1278 LoRa at 37,000bps air rate, 14940mS, an achieved air rate of 35,057bps

	nRF24L01 at 2,000,000bps air rate, 330mS, an achieved data rate of 1,598,439bps 
	nRF24L01 at 1,000,000bps air rate, 660mS, an achieved data rate of 798,003bps 
	nRF24L01 at 250,000bps air rate, 2630mS, an achieved data rate of 199,577bps 

Point to note is that for all the transfers the maximum packet size allowed by the device was used. This was 255 bytes for the LoRa devices, 127 bytes for the SX1280 in FLRC mode and 32 bytes for the nRF24L01. Arduino used was a DUE.

Of particular interest was the results from the SX1280 in FLRC mode, a transfer time 3 times faster than what is possible with LoRa. It would be interesting to compare the distances that FLRC and LoRa can achieve. 

Also note that the blind transmit achieved data rates listed above is considerably more than that for a practical application. The SX128X in LoRa mode might demonstrate the ability to send data at 173,836bps, but by the time you add in the overhead of reading the required data (from SD for instance) transmitting it and waiting for an acknowledge to be sure it was received, the achieved data rate dropped to circa 111,296bps. 

## Performance Tuning

The example programs contain some parameters that need to be optimised for a particular set of LoRa or FLRC parameters depending on the on-air rate. Consider the sequence of sending a segment;

1. The transmitter reads the next segment from SD card.
2. The segment is loaded into the LoRa devices buffer.
3. The segment is transmitted as a packet.
4. When the transmission completes, the transmitter switches the LoRa device to listening for the acknowledge. 
5. The transmitter waits a predefined time for the acknowledge.
6. If the acknowledge is received within the time allowed the transmitter starts the process again for the next segment.
7. If there is no valid acknowledge received before the time-out, the segment is re-transmitted.

The data transfer process has to allow for the acknowledge from the receiver not being picked up by the transmitter. In the normal sequence there is a delay at the receiver whilst the received segment is saved to the SD card. Whilst this save to SD card is taking place the transmitter normally has time to swap across from transmit to receive and be ready to pick up the acknowledge. If the acknowledge is not received due to a packet error caused by interference for instance, when the transmitter re-sends the same segment, the receiver recognises this and does not save the segment to SD card. Thus there is only a small delay before the second acknowledge is sent out. Its possible in these circumstances that the transmitter is not actually ready to receive when the acknowledge starts to send, and its missed again, and again, and again.

To get out of this potentially stuck situation there are several parameters that can be changed in the DTSettings.h file for optimum performance.   

	const uint32_t ACKsegtimeoutmS = 75;            //mS to wait for receiving an ACK before re-trying transmit segment
	const uint32_t ACKopentimeoutmS = 250;          //mS to wait for receiving an ACK before re-trying transmit file open
	const uint32_t ACKclosetimeoutmS = 250;         //mS to wait for receiving an ACK before re-trying transmit file close
	const uint32_t DuplicatedelaymS = 10;           //ms delay if there has been an duplicate segment or command receipt
	const uint32_t NoAckCountLimit = 250;           //if no NoAckCount exceeds this value - restart transfer 

### ACKsegtimeoutmS

This is the mS to wait for the transmitter to receive an ACK before re-trying the segment transmit. For maximum throughput this time should be as short as possible. A long time-out here, say 1000ms, would slow the data transfer considerably if there are significant numbers of packet errors with the reception of the acknowledges. A time-out of 75mS works reliably for the faster 200kbps+ on-air data rates but would need to be increased for slower on-air data rates. 

For slower LoRa or FLRC on-air rates the ACKsegtimeoutmS time of 75mS, might not be enough time for the acknowledge to be received so the transmitter might keep complaining it has received no acknowledge. The receiver might also keep saying it has already received a particular segment. If in doubt increase the ACKsegtimeoutmS to a much bigger figure, 1 second maybe, then keep cutting it down until problems start to appear and then go back and increase the value a bit.  

### ACKopentimeoutmS

This is the mS to wait for the transmitter to receive an ACK before re-trying the file open request to the receiver again. The receiver might take a few 100s of mS to open the requested file on SD, so increase this acknowledge time-out if there are repeated requests sent by the transmitter to open a new file. 


### ACKclosetimeoutmS

This is the mS to wait for the transmitter to receive an ACK before re-trying the fileclose request to the receiver again. The receiver might take a few 100s of milliseconds to close the requested file on SD, so increase this ack timeout if there are repeated requests sent by the transmitter to close the file. Closing a file is the end of the data transfer.

### DuplicatedelaymS

This is an additional delay that can be used to compensate for the quicker ack response of the receiver when a duplicate segment write is sent, see discussion above. Also applies to duplicate file open or close requests. If a repeated sequence of no ack received messages are reported by the transmitter try adding a few 10s of mS extra delay with this parameter.  

### NoAckCountLimit

From the start of the data transfer a running count is kept of how many transmissions are sent but no acknowledge is received. If the receiver gets stuck somehow then the number of missed acknowledges can increase quickly, depending on the time-out settings. If the number of missed acknowledges reaches the NoAckCountLimit set then the data transfer is restarted. This limit is intended as a backstop to prevent the transmitter endlessly sending segments or requests.  

## Example Programs

The programs used for the SX1280 data transfer applications and supporting library code are in the [**SX12XX-LoRa Library**](https://github.com/StuartsProjects/SX12XX-LoRa).

Example programs **221\_LoRa\_Packet\_Monitor** and **222\_FLRC\_Packet\_Monitor** will allow you to monitor the send and acknowledge exchange of a data file transfer. Best to use a reasonably fast Arduino such as a DUE.

**231\_Data\_Transfer\_Test\_Transmitter** and **232\_Data\_Transfer\_Test\_Receiver** will simulate a transfer of a set file size. No SD card is needed for receiver or transmitter. The programs allow you in a simple to test different LoRa or FLRC settings to see how reliable they are in a particular environment. 

Programs **233\_SDfile\_Transfer\_Transmitter** and **234\_SDfile\_Transfer\_Receiver** are the applications that copy an image from the SD card of one Arduino to the SD card of another. Arduino DUEs were used in the tests. These data transfer examples can be switched between LoRa and FLRC modes by setting appropriate #defines at the start of the programs. 

**Program 237\_Array\_Transfer\_Transmitter** is a demonstration of sending a memory array as a data transfer. The array is first populated with an image from SD card by the transmitter. The receiver saves the received data transfer, the array, to an SD card file.  

<br>

### **Stuart Robinson**
### **November 2021**