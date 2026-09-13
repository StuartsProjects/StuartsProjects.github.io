---
layout: post
title: "SX126X Updated Library"
date: "2026-09-09"
---


There now a revision of the original SX126XLT LoRa library called SX126X that introduces some changes;

1 The SPI bus in use can be specified, this is very useful for Microcontrollers that have multiple SPI busses such as the ESP32 series

    setSPI(SPIClass &spi);

2 The maximum SPI frequency can be changed via a function call.

    setSPIFrequency(uint32_t frequency);

3 Some library functions print to the serial port for debugging purposes, this port can be changed from the standard Serial port
to other Serial ports such as Serial1, Serial2 etc that are supported either as hardware serial ports or software serial ones.

    setSerial(Stream *streamObject);

4 The setupLoRa() function has been expanded to allow a particular TCXO voltage to be set and the DIO2 RF switch operation to be specified.

    setupLoRa(uint32_t frequency, int32_t offset, uint8_t modParam1, uint8_t modParam2, uint8_t modParam3, uint8_t modParam4, uint8_t tcxoVoltage, uint8_t rfswitch);

5 The RXDuty cycle power saving receive mode is supported for some of the library receive functions

    receiveRxDutyCycle(uint8_t *rxbuffer, uint8_t size, uint8_t wait, uint32_t rxus, uint32_t sleepus);
    receiveSXBufferRxDutyCycle(uint8_t startaddr, uint8_t wait, uint32_t rxus, uint32_t sleepus);

6 A setPins function is added so that pins can be defined for LoRa module without a device reset



    setPins(int8_t pinNSS, int8_t pinNRESET, int8_t pinRFBUSY, int8_t pinDIO1, int8_t pinRXEN, int8_t pinTXEN, uint8_t device);





## Note:
The updated versions of the SX126XLT library files have been renamed so that the old example files will still work as normal. The updated files are now included in the Github repository and are named;

    SX126X.h
    SX126X.cpp
    SX126X_Definitions.h

If you want to use one of the library examples that originally used the SX126XLT library files you need to change the file the example program uses for the library, you need to change this line in an example;

    #include <SX126XLT.h>
    
    to
    
    #include <SX126X.h> 


### Stuart Robinson 
### September 2026


