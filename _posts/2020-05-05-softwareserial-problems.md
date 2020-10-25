---
layout: post
title: "SoftwareSerial problems"
date: "2020-05-05"
---

Software serial is an Arduino library that allows you to use serial devices, such as GPSs, via 2 digital pins without needing a specific hardware serial port. The Arduino Pro Minis for instance use the ATmega328p processor which has only one hardware serial port but that is normally reserved for downloading programs and for the Serial Monitor output. To use a serial device such as a GPS on a Pro Mini then requires the use of the SoftwareSerial library.

Some years back I wrote a high altitude balloon (HAB) tracking application and the tracker transmitter need to configure the GPS for high altitude use, the UBLOX GPSs stop working above 9,000M otherwise. I noticed that occasionally the configuration of the GPS was failing. I amended the program to show (on the Serial Monitor) the configuration commands being sent to the GPS and then polled the GPS to report its what should be new configuration settings. I noticed then that occasionally the reply from the GPS (containing its stored configuration settings) was being missed, there was a time-out.

These time-outs occurred with the standard SoftwareSerial.h library but not with the NeoSWSerial.h library that you can get from here;

**[NeoSWSerial](https://github.com/SlashDevin/NeoSWSerial)**

I even added a comment about NeoSWSerial to the HAB tracker Arduino sketch;

“this library is more reliable at GPS init than software serial”

### The SX12XX Library

I recently started converting the original HAB tracking application to run on my recent SX12XX Library which you can see here;

**[SX12XX Library](https://github.com/StuartsProjects/SX12XX-LoRa)**

The SX12XX library supports the SX1262 and SX1268 LoRa® devices which use temperature compensated crystal oscillators (TCXO). The use of the TCXO makes the LoRa® device much more frequency stable than the older SX127X devices.

Mostly the SX127X devices manage OK without a TCXO but when sending FSK RTTY for instance there can be significant frequency drift in the output signal. This means that decoding the RTTY on a receiver required a lot of manual intervention as there was a need to constantly re-tune the receiver. That a high altitude balloon tracker would be expected to work from maybe +25C to -25C (or lower) did make the drift problem of the RTTY worse and more of a challenge to receive. Over that temperature range the HAB tracker transmitter might drift 2000hz or more in frequency whilst the SX126X drifts around 200hz, making decoding FSK RTTY very much easier. The first step however was to get FSK RTTY working on the new SX127X part of the library, so one again I faced the problem of using SoftWareSerial and configuring the GPS reliably.

I changed the GPS configuration routines to make them more robust, but I was still getting time-outs, i.e. no apparent replies from the GPS to the configuration commands. I started monitoring the output from the GPS (during configuration) and after a lot of checking, I could see that the GPS was getting the configuration commands and was actually sending out the correct replies and acknowledgements. The Arduino program was however managing to miss these replies. Again there was no problem at all with NeoSWSerial, no time-outs, no problems.

I eventually came up with a simple program that demonstrates the issue. The program reads a character from the GPS then prints it to the Serial monitor as HEX. This results in two characters being printed to serial monitor, for every one received from the GPS. The program also tests for a line feed character (0x0A) which the GPS sends at the end of the its output of each NMEA sentence. On receipt of this line feed character a carriage return is sent to the Serial Monitor so that the NMEA sentences, in HEX, should all appear on separate lines. With the GPS running at 9600baud and the Serial monitor running at 115200 baud the Serial Monitor output ought to be able to keep up, and indeed it does, when your using NeoSWSerial, the program used is below;


	//#include <SoftwareSerial.h>
	//SoftwareSerial GPS(RXpin, TXpin);

	#include <NeoSWSerial.h>        //get library here https://github.com/SlashDevin/NeoSWSerial  
	NeoSWSerial GPSserial(A3, A2); //RXpin,TXpin

	void loop()
	{
	uint8_t GPSchar;
  
	  while (GPSserial.available())
  	  {
         GPSchar = GPSserial.read();

         if (GPSchar == 0x0A)
         {
         Serial.println();  
         }
         else
         {
         Serial.print(GPSchar,HEX);  
         }
 	  }
    }
   
	void setup()
	{
	GPSserial.begin(9600);
	Serial.begin(115200);
	Serial.println("GPS_Echo_HEX Starting");
	}`


This is the output that the use of the NeoSWSerial produces, only a limited number of the left most characters are shown;

	GPS_Echo_HEX Starting
	2447504753562C332C332C31322C32342C38382C3334362C34302C
	244750474C4C2C353133302E36363733382C4E2C30303331312E38
	244750524D432C3037353632372E30302C412C353133302E363637
	2447505654472C2C542C2C4D2C302E3033372C4E2C302E3036382C
	2447504747412C3037353632372E30302C353133302E3636373339
	2447504753412C412C332C31302C31372C32342C31322C31352C31


Note that the first character is HEX 0x24 which is ASCII '$' so the program is correctly picking up the line feed character at the end of a NMEA sentence and then starting to print the next NMEA sentence (which starts with $) on a new line and no characters are missed. However look at the output when using SoftwareSerial;

	2447505654472C2C542C2C4D2C302E3032392C4E2C302E30352C41	
	2447505654472C2C542C2C4D2C302E3035372C4E2C302E31302C41
	50562C313030302C2C2C3230323931323131312C2AD47562C31313
	50562C31323833343231322C2C2C2C3832322C2C3337D474C35303
	2447505654472C2C542C2C4D2C302E3033342C4E2C302E30362C41
	472C3330373432353932303533382C35302C2E30
	50562C313030302C30383834323931323131312C2AD4753332C313
	2447505654472C2C542C2C4D2C302E3034322C4E2C302E30372C41
	50562C313030302C2C2C3230323931323131312C37
	50562C313133312C2C2C2C372C2C2C35363038343337


Not so good. If the characters are artificially lined up like so;

	24475056544722C542C2C4D2C302E3032392C4E2C302E30352C41
	2447505654472C2C542C2C4D2C302E3035372C4E2C302E31302C41
        50562C313030302C2C2C3230323931323131312C2AD47562C31313
        50562C31323833343231322C2C2C2C3832322C2C3337D474C35303
	2447505654472C2C542C2C4D2C302E3033342C4E2C302E30362C41
      472C3330373432353932303533382C35302C2E30
        5056 2C313030302C30383834323931323131312C2AD4753332C313
    2447505654472C2C542C2C4D2C302E3034322C4E2C302E30372C41
        50562C313030302C2C2C3230323931323131312C37
        50562C313133312C2C2C2C372C2C2C35363038343337


You can see that SoftwareSerial is often missing the first one or two characters at the beginning of a sentence. This would explain the frequent time-outs seen in the HAB tracker program. It was sending the configuration command to the GPS and printing each character to the Serial Monitor as it goes. The program then looks for a 0xB5 character from the GPS which is the start of the its reply, then the details indicating either a ACK (good) or a NAK (fail). The time-outs then are apparently occurring because SoftwareSerial is missing the 0xB5 reply when there are standard Serial.print() operations in progress.

Once the problem was identified, all that was needed was to go through the code and make sure that there was no background Serial.print() operations in progress, by using Serial.flush() at appropriate points.

In the case of sending the configuration commands to the GPS, the software was changed so that the command string in HEX, was first printed to the Serial monitor, then a Serial.flush() was executed. Thus when SoftwareSerial sent the command to the GPS there was no Serial.print() in progress.

This resulted in a great improvement in reliability and the time-outs waiting for a response from the GPS were no longer seen.

However there was still occasions when the output to the Serial Monitor output was broken up. The code was further revised so that SoftwareSerial was only running when the GPS was being read, as in when acquiring a fix or it was being configured or there was a reply being scanned for. The SoftwareSerial instance used, called GPSserial was stopped with a GPSserial.end(); command and started again with GPSserial.begin(GPSBaud);

When all this was done, calm and stability returned to the universe and SoftwareSerial seemed happy, no more time-outs or messed up serial prints. Perhaps the major lesson here is that if you must use SoftwareSerial, don't leave it running in the background when its not needed.

I would still be happier using NeoSWSerial, however, there seems to have been a lot of care put into writing it and making it robust and interoperable with other processes.

Stuart Robinson

May 2020.
