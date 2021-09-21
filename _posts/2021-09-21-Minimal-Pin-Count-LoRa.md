---
layout: post
title: "Minimal Pin Count LoRa"
date: "2021-09-21"
---

To use an SX127x LoRa device you normally use 6 pins;

SPI SCK, SPI MISO, SPI MOSI, SPI NSS, NRESET, DIO0

The 3 SPI bus pins and the NSS device select are needed to read and write to the registers on the SX127x LoRa device. NRESET is normally used to reset the LoRa device registers to default on power up, but the device can manage that power up on its own if you leave NRESET not connected. 

DIO0 on the SX127x is normally configured to go high on a packet reception (RXdone) or when a transmission has finished (TXdone). The micro controller normally waits for this signal by polling an IO pin attached to DIO0. 

The normal library code to wait for the receipt of a packet is, from the **[SX12xx-LoRa](https://github.com/StuartsProjects/SX12XX-LoRa)** library;

while (!digitalRead(\_RXDonePin) && ((uint32\_t) (millis() - startmS) < rxtimeout));

Which polls the \_RXDonePin (normally DIO0) or waits for a time-out. 

You can dispense with DIO0, leave it floating, and instead of waiting for DIO0 pin to go high you can use this bit of code;  

 while (!isRXdoneIRQ() && ((uint32_t) (millis() - startmS) < rxtimeout));

Where **isRXdoneIRQ()** is;

    bool isRXdoneIRQ()
    {
      return (readRegister(REG\_IRQFLAGS) & IRQ\_RX\_DONE);
    }
    
There is a matching **isTXdoneIRQ()** function for polling the registers for when a transmit (TXdone) is complete. 

So in practise you can use an SX127x LoRa device with only 4 IO pins, which is handy because if you try to use a LoRa device on an **ESP32CAM**, you are really short of pins, if you also want to use the SD card. 

To make the use of LoRa for these limited pin situations I have written some 'IRQ' routines for the  [**SX12XX-LoRa Library**](https://github.com/StuartsProjects/SX12XX-LoRa).
    
    uint8_t transmitIRQ(uint8_t *txbuffer, uint8_t size, uint32_t txtimeout, int8_t txPower, uint8_t wait);
	uint8_t receiveIRQ(uint8_t *rxbuffer, uint8_t size, uint32_t rxtimeout, uint8_t wait);
    uint8_t transmitSXBufferIRQ(uint8_t startaddr, uint8_t length, uint32_t txtimeout, int8_t txpower, uint8_t wait);
	uint8_t receiveSXBufferIRQ(uint8_t startaddr, uint32_t rxtimeout, uint8_t wait);
	uint8_t transmitSXReliableIRQ(uint8_t startaddr, uint8_t length, uint16_t networkID, uint32_t txtimeout, int8_t txpower, uint8_t wait);
    uint8_t receiveSXReliableIRQ(uint8_t startaddr, uint16_t networkID, uint32_t rxtimeout, uint8_t wait );
    uint8_t sendSXReliableACKIRQ(uint8_t startaddr, uint8_t length, uint16_t networkID, uint16_t payloadcrc, int8_t txpower);
    uint8_t waitSXReliableACKIRQ(uint8_t startaddr, uint16_t networkID, uint16_t payloadcrc, uint32_t acktimeout);
	uint8_t transmitDTIRQ(uint8_t *header, uint8_t headersize, uint8_t *dataarray, uint8_t size, uint16_t networkID, uint32_t txtimeout, int8_t txpower, uint8_t wait);
    uint8_t receiveDTIRQ(uint8_t *header, uint8_t headersize, uint8_t *dataarray, uint8_t size, uint16_t networkID, uint32_t rxtimeout, uint8_t wait );
    uint8_t sendACKDTIRQ(uint8_t *header, uint8_t headersize, int8_t txpower);
    uint8_t waitACKDTIRQ(uint8_t *header, uint8_t headersize, uint32_t acktimeout);

Apart from not needing the DIO0 or NRESET pins the above library functions operate in the same way as the non-IRQ equivalents.

These IRQ functions do not need NRESET or DIO0 defined and you can initialise the LoRa device like this;

	LoRa.begin(NSS, DEVICE_SX1278);

There are various examples that use this limited pin approach;

    3_LoRa_TransmitterIRQ
    4_LoRa_ReceiverIRQ
    8_LoRa_LowMemory_TXIRQ
    9_LoRa_LowMemory_RXIRQ
    219_Reliable_Transmitter_Data_RequestorIRQ
    220_Reliable_Receiver_Data_RequestorIRQ
	235_SDfile_Transfer_TransmitterIRQ


**Stuart Robinson**

**September 2021**