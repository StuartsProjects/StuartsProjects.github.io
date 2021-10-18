---
layout: post
title: "Generating UBLOX GPS Configuration Messages"
date: "2018-08-26"
---

Some of the configurations for a UBLOX GPS require the creation and sending of PUBX (ASCII) or PBX (binary) configuration messages. 

You can work the required bytes to send from the Ublox datasheets, with some difficulty, but you then need to work out what the checksums are for the string of characters to send, the checksum is appended to the end of the command string.  For instance to change the GPS to a navigation output rate from the 1Hz default to 10Hz  you would send the following stream of hex bytes as a PBX command;

0xB5, 0x62, 0x06, 0x08, 0x06, 0x00, 0x64, 0x00, 0x01, 0x00, 0x01, 0x00, 0x7A, 0x12

The 0x12 at the end is the checksum of the previous 13 characters. 

There is a tool provided by UBLOX that will do all this configuration for you, its called U-Center and runs under Windows. I recommend you download and install it.

You can connect U-Center to a running GPS with a serial adapter and you you will then be able to see all the information on satellites, signal strengths etc. More importantly you can also make configuration changes and test the effect. If your GPS has EEPROM or battery backup you can save the changes too. 

U-Center will also tell you the string of hex characters to send for a particular configuration so you can record them and then send from Arduino sketches. As an example this post will go through the steps of being able to change the navigation rate from 1Hz to 10Hz.

First connect your GPS to a PC, start up U-Center and set it up for your GPS;

Set \Receiver\Port to the COM port your GPS is connected to.
Set \Receiver\Baudrate to the baud rate for your GPS, normally 9600.
Set \Receiver\Generation to the generation of GPS being used, ublox-6 in this case. 

If the GPS is working correctly you should see the characters coming from the GPS if you select \View\Text Console, the picture below shows you what you see when the GPS has just been powered up;

<br>

![](/images/Ucenter1.jpg)

<br>

To configure the update rate select \View\MessagesView. Scroll down the list of NMEA messages on the left edge of the window and open up the 'UBX' messages list. Then select 'CFG (Config)' and then 'RATE (Rates)'. Next at the bottom of the messages view window select the 'Show Hex Toggle' button, highlighted in red in the picture below;

<br>

![](/images/Ucenter2.jpg)

<br>

The Upper right part of the Window shows the details of the message rate configuration possible, and the window under that shows the list of HEX bytes to send to enact that command. 

Change the measurement period to 100mS and the window looks like this;

<br>

![](/images/Ucenter3.jpg)

<br>

If you press the 'Send' button on the bottom left of the U-center screen then the command is sent to the GPS and the update rate should change. Note that for the change to take full effect you will likely need to turn of most of the NMEA sentences sent by the GPS till only GPGGA and GPRMC are sent, see the MSG (Messages) configuration.

You can copy the HEX data from the bottom right window;

B5 62 06 08 06 00 64 00 01 00 01 00 7A 12

You then need to copy this into a data array definition for Arduino such as like this;

	const PROGMEM  uint8_t Navrate10hz[] = {0xB5, 0x62, 0x06, 0x08, 0x06, 0x00, 0x64, 0x00, 0x01, 0x00, 0x01, 0x00, 0x7A, 0x12};

Within an Arduino Sketch you can now send the sequence of bytes above as a configuration command to the GPS. 


	GPS_SendConfig(Navrate10hz, 14);   //name of PROGMEM array and length


	void GPS_SendConfig(const uint8_t *Progmem_ptr, uint8_t arraysize)
	{
	uint8_t byteread, index;
	
	Serial.print(F("GPSSend  "));

	for (index = 0; index < arraysize; index++)
  	{
	byteread = pgm_read_byte_near(Progmem_ptr++);
	if (byteread < 0x10)
	{
	Serial.print(F("0"));
	}
	Serial.print(byteread, HEX);
	Serial.print(F(" "));
  	}

	Serial.println();
	Progmem_ptr = Progmem_ptr - arraysize;  //set Progmem_ptr back to start

	for (index = 0; index < arraysize; index++)
  	{
	byteread = pgm_read_byte_near(Progmem_ptr++);
	GPS.write(byteread);
	}
	delay(100);

	}


Edit: 09/09/21 
The sketch that demonstrates the configuration of the Ublox GPS is '90\_UBlox\_GPS\_Configuration.ino' found in the SX12xx library \examples\Hardware_Checks folder;

[https://github.com/StuartsProjects/SX12XX-LoRa](https://github.com/StuartsProjects/SX12XX-LoRa "SX12xx Library")

<br>


### Stuart Robinson
### August 2018