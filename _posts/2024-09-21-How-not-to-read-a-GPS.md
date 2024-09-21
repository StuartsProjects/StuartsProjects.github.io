---
layout: post
title: "How not to read a GPS with TinyGPSplus"
date: "2024-09-21"
---

I often see posts on forums where users are having problems reading the location parameters from a GPS using the TinyGPSplus library. Latitude and longitude or altitude can report as 0 for instance. Or you can have a situation where the location remains static for some time and only changes sporadically. 

This is a common bit of code used to read a GPS with TinyGPSplus;

    while(GPSserial.available() > 0)
    {
       if (gps.encode(GPSserial.read()))
       {
          if (gps.location.isValid())
          {
             display_location();
          }
     }

Unfortunately that code will fail in several circumstances and should not really be used.

## Failures using isValid() 

When the TinyGPSplus library does it's first read and successful decode of the location information from the GPS after the sketch starts, it sets a flag that identifies the latitude, longitude, altitude etc as valid. This valid flag remains true until the library is restarted due to a program reset or power down. So if after the first read of a location from the GPS, the GPS moves to a poor reception area or goes faulty and stops getting location fixes, the latitude longitude altitude etc will still be reporting as valid. If the GPS is no longer working what is the point of displaying the unchanged location on the Serial monitor, attached display or logging it to SD ? 

Avoid using gps.location.isValid() to decide whether to print, display or log location data, you cannot directly tell (with isvalid()) if the GPS has lost reception or is just actually stationary. 

## Issues with encode()

encode() returns true when a GPS sentence is read and it has a valid checksum. However, this does not mean, directly, that the latitudue longitude height etc have new updated values. 

TinyGPSplus gets its position and other data only from the $GPRMC and $GPGGA NMEA sentences and these are the only two sentences needed. However as default a GPS puts out quite a few more NMEA sentences, this is a typical sequence;

    $GPGSA,A,3,27,18,23,26,08,10,07,16,,,,,1.42,1.12,0.87*08
    $GPGSV,3,1,12,16,74,202,37,27,67,287,40,18,42,058,38,23,40,103,34*7C
    $GPGSV,3,2,12,26,39,163,35,08,32,279,30,10,27,142,24,07,20,314,26*73
    $GPGSV,3,3,12,02,04,053,,15,03,054,,21,01,229,,30,01,342,*70
    $GPRMC,153535.000,A,####.#####,N,####.#####,W,0.56,283.08,240323,,,A*7F
    $GPVTG,283.08,T,,M,0.56,N,1.04,K,A*3A$GPGGA,153536.000,####.#####,N,####.#####,W,1,8,1.12,29.8,M,49.9,M,,*7F
    $GPGLL,####.#####,N,####.#####,W,153536.000,A,A*47


(The actual latitude and longitude values have been replaced with # characters)

So eight GPS NMEA sentences but only two are used by the TinyGPSplus library for data.

Thus when a bit of typical Arduino code does this;

	while (GPSserial.available() > 0)
    {
      if (gps.encode(GPSserial.read()))
      {
	  displayInfo();
	  }

gps.encode() returns true for all eight NMEA sentences and the program then calls something like displayInfo() for all eight sentences which may in turn print out, display or log all this stuff;

      Serial.print(Latitude, 5);
      Serial.print(F(","));
      Serial.print(Longtitude, 5);
      Serial.print(F(","));
      Serial.print(Altitude, 1);
      Serial.print(F(","));
      Serial.print(Sats);
      etc.
      etc.
      etc.

That's a lot of Serial.print() and 6 times out of 8 there has been no actual change in the data printed because, remember, changes in the data printed only occur when the $GPRMC or $GPGGA sentences themselves are encoded. So why waste program time printing out stuff on the Serial monitor or writing to a display when its not actually changed ?????

## More serious issues with encode()

Consider the contents of the two GPS sentences that TinyGPSplus uses;

#### $GPRMC sentence contents;

    Current time (if available; UTC)
    Position status (A for valid, V for invalid) 
    Latitude compass direction 
    Longitude (in DDDMM.MMM format) 
    Longitude compass direction 
    Speed (in knots per hour) 
    Heading 
    Date (DDMMYY) 
    Magnetic variation 
    Magnetic variation direction 
    The checksum validation value (in hexadecimal) 
    
#### $GPGGA sentence contents;
    
    Current time (if available; UTC) 
    Latitude (in DDMM.MMM format) 
    Latitude compass direction 
    Longitude (in DDDMM.MMM format) 
    Compass direction 
    Fix type (0 for no fix, 1 for GPS, 2 for DGPS) 
    Number of satellites used for fix 
    Horizontal dilution of precision 
    Altitude above mean sea level 
    Altitude units (M for meters) 
    Height of mean sea level above WGS-84 earth ellipsoid 
    Units of the above geoid separation (M for meters) 
    Time since last differential correction (ignored if inactive) 
    Differential station ID (ignored if inactive) 
    The checksum validation value (in hexadecimal) 

Now it was mentioned above that using encode() to decide to print, display or log stuff can result in a lot of time being spent in effect not updating anything. 

However consider what happens when if the GPS data is as follows;

    $GPGSA,A,3,27,18,23,26,08,10,07,16,,,,,1.42,1.12,0.87*08
    $GPGSV,3,1,12,16,74,202,37,27,67,287,40,18,42,058,38,23,40,103,34*7C
    $GPGSV,3,2,12,26,39,163,35,08,32,279,30,10,27,142,24,07,20,314,26*73
    $GPGSV,3,3,12,02,04,053,,15,03,054,,21,01,229,,30,01,342,*70
    $GPRMC,153535.000,A,####.#####,N,####.#####,W,0.56,283.08,240323,,,A*7F
    $GPVTG,283.08,T,,M,0.56,N,1.04,K,A*3A$GPGGA,153536.000,####.#####,N,####.#####,W,1,8,1.12,29.8,M,49.9,M,,*7F
    $GPGLL,####.#####,N,####.#####,W,153536.000,A,A*47

(The actual latitude and longitude values have been replaced with # characters)

So after the $GPRMC sentence is encoded your sketch goes of and spends a lot of time printing, displaying or logging stuff. If your sketch spends too long away from reading the GPS characters by the time all the printing, displaying or logging stuff has finished, the $GPGGA sentence above may have been missed. Net result of that is that although the time, location, speed and date will have updated (since they are in the $GPRMC sentence) the altitude, sats and course will not have updated ((since they are in the missed $GPGGA sentence).

This problem with encode() is a particular issue if the GPS has been configured to only send out the $GPRMC and $GPGGA sentences, which is a common set-up. Then when $GPRMC is encoded you have not got long time wise to get back to reading the GPS so that the $GPGGA sentence is encoded correctly, it can so easily be missed. 

### Issues with softwareserial

Using encode() to decide when to print stuff to the serial monitor can cause problems when using softwareserial to read characters from the GPS. 

When using softwareserial to read a GPS conflicts with other interrupt driven devices such as SPI, I2C and Serial print that are used on the Arduino can cause problems. Such software conflicts can cause softwareserial to miss read characters from the GPS so the decoding of NMEA sentences containing the latitude and longitude does not takes place. 

It thus makes sense to only use Serial prints after the sketch has actually  updated the location data, particularly with slower Arduinos such as the 3.3V Pro Mini which runs at 8Mhz. What you want to avoid is having softwareserial attempting to read the $GPRMC and $GPGGA sentences when there are still Serial.prints() running. 


##So how to read a GPS robustly ?

TinyGPSplus has a method that allows you to check whether data such as time, location, altitude etc has actually been updated since the last time the location data was used. Its the isUpdated() method and it is used like this; 

	void loop()
	{
 	   if (GPSserial.available() > 0)
  	   {
	   gps.encode(GPSserial.read());
  	   }

  	   if (gps.location.isUpdated())
  	   {
	   displayInfo();
  	   }
	}


What is happening here is that loop() keeps reading characters from the GPS until the location is actually updated and only then does the sketch go off and spend time printing, displaying or logging stuff. 

Now that is fine if the only information you want displayInfo() to put out is latitude and longitude. However, as mentioned above altitude and satellites are only in $GPGGA, so TinyGPSplus can report true for gps.location.isUpdated() and then report 0 for altitude if its only decoded the $GPRMC sentence. 

So if we change the isUpdated() check to look like this;

    void loop()
	{
 	  if (GPSserial.available() > 0)
  	  {
	  gps.encode(GPSserial.read());
  	  }

  	  if (gps.speed.isUpdated() && gps.satellites.isUpdated())
  	  {
	  displayInfo();
  	  }
	}

We can be sure that both $GPRMC and $GPGGA have recently been encoded since speed is only in $GPRMC and satellites is only in $GPGGA 

Note also that **no time is wasted printing stuff** until both $GPRMC and $GPGGA have been encoded. Also we now usually have a much longer period to print stuff before the next $GPRMC and $GPGGA are sent out by the GPS. 

## TIP: Read the GPS only when you want new data

In a lot of programs using a GPS, most trackers for instance, there is no particular need to be constantly reading the characters from a GPS. You might just want to read a GPS for an updated location on a periodical basis then write the new data to a display or transmit the GPS data with a radio device such as LoRa. You might want to do this only every 60 seconds or so.

When you want to get updated GPS data, on waking up from a deep sleep for instance, then just call a function that starts reading the GPS characters and keep passes them to the GPS library until the location etc is actually updated. 

One way of doing this is used in the program listing below, a function is created called gpsWaitFix(uint32_t waitmS). This is used to read characters from the GPS but with a time-out. If the GPS does not have a new location fix in say 5000mS control is passed back to the program and reading the GPS is then not a blocking function. If the routines were used in a tracker this non-blocking function would allow you to transmit the last known location but with a flag added to the tracker packet indicating that the GPS is not updating for now. 


## Robust GPS reading with Hardware serial

```

/*******************************************************************************************************
  Programs for Arduino - Copyright of the author Stuart Robinson - 20/9/24

  This program is supplied as is, it is up to the user of the program to decide if the program is
  suitable for the intended purpose and free from errors.
*******************************************************************************************************/

/*******************************************************************************************************
  Program Operation -  This program is a GPS checker. It demonstrates robust code for reading a GPS with
  the TinyGPSplus library and a Hardware Serial port. The program assumes the GPS in on port Serial1.
  If the GPS is on a different hardware serial port ensure you change the #define GPSserial Serial1

  In setup() check that the hardware serial port for the GPS has the correct baud rate i.e. for 9600
  baud you should have GPSserial.begin(9600);  

  Serial monitor baud rate is set at 115200.
*******************************************************************************************************/

#include <TinyGPS++.h>                             //get library here > http://arduiniana.org/libraries/tinygpsplus/
TinyGPSPlus gps;                                   //create the TinyGPS++ object

#define GPSserial Serial1                          //define hardware serial port GPS is on here

float GPSLat;                                      //Latitude from GPS
float GPSLon;                                      //Longitude from GPS
float GPSAlt;                                      //Altitude from GPS
uint8_t GPSSats;                                   //number of GPS satellites in use
uint32_t startGetFixmS;
uint32_t endFixmS;


void loop()
{
  startGetFixmS = millis();                        //record time reading GPS for fix starts

  if (gpsWaitFix(5000))
  {
    GPSLat = gps.location.lat();
    GPSLon = gps.location.lng();
    GPSAlt = gps.altitude.meters();
    GPSSats = gps.satellites.value();
    displayInfo();

  }
  else
  {
    Serial.print(F("Timeout - No GPS Fix "));
    Serial.print( (millis() - startGetFixmS) / 1000 );
    Serial.println(F("s"));
  }
  Serial.println();
}


bool gpsWaitFix(uint32_t waitmS)
{
  //waits a specified number of millis for a fix, returns true for updated fix

  uint32_t startmS;
  uint8_t GPSchar;

  Serial.print(F("Wait GPS Fix "));
  Serial.print(waitmS);
  Serial.println(F("mS"));

  startmS = millis();

  while ( (uint32_t) (millis() - startmS) < waitmS)           //allows for millis() overflow
  {
    if (GPSserial.available() > 0)
    {
      GPSchar = GPSserial.read();
      gps.encode(GPSchar);
      Serial.write(GPSchar);
    }

    if (gps.speed.isUpdated() && gps.satellites.isUpdated()) //ensures that GGA and RMC sentences have been received
    {
      endFixmS = millis();                                   //record the time when we got a GPS fix
      Serial.println();
      Serial.print(F("Fix time "));
      Serial.print(endFixmS - startmS);
      Serial.println(F("mS"));
      return true;
    }
  }
  return false;
}


void displayInfo()
{

  Serial.print(F("New GPS Fix "));
  Serial.print(F("Lat,"));
  Serial.print(GPSLat, 6);
  Serial.print(F(",Lon,"));
  Serial.print(GPSLon, 6);
  Serial.print(F(",Alt,"));
  Serial.print(GPSAlt, 1);
  Serial.print(F("m,Sats,"));
  Serial.print(GPSSats);
  Serial.println();
}


void setup()
{
  GPSserial.begin(9600);                        //hardware serial port for GPS
  Serial.begin(115200);
  Serial.println();
  Serial.println(F("Robust_GPS_reading_with_HardwareSerial Starting"));
  Serial.println();

  startGetFixmS = millis();
}

```

## Robust GPS reading with SoftWare serial

/*******************************************************************************************************
  Programs for Arduino - Copyright of the author Stuart Robinson - 20/9/24

  This program is supplied as is, it is up to the user of the program to decide if the program is
  suitable for the intended purpose and free from errors.
*******************************************************************************************************/

/*******************************************************************************************************
  Program Operation -  This program is a GPS checker. It demonstrates robust code for reading a GPS with
  the TinyGPSplus library and SoftwareSerial.

  In setup() check that the hardware serial port for the GPS has the correct baud rate i.e. for 9600
  baud you should have GPSserial.begin(9600);

  Serial monitor baud rate is set at 115200.
*******************************************************************************************************/

#include <TinyGPS++.h> //get library here > http://arduiniana.org/libraries/tinygpsplus/
TinyGPSPlus gps;   //create the TinyGPS++ object

#define RXpin 3//pin number for GPS RX input into Arduino - TX from GPS
#define TXpin 2//pin number for GPS TX output from Arduino- RX into GPS

#include <SoftwareSerial.h>
SoftwareSerial GPSserial(RXpin, TXpin);

float GPSLat;  //Latitude from GPS
float GPSLon;  //Longitude from GPS
float GPSAlt;  //Altitude from GPS
uint8_t GPSSats;   //number of GPS satellites in use
uint32_t startGetFixmS;
uint32_t endFixmS;


void loop()
{
  startGetFixmS = millis();//record time reading GPS for fix starts

  if (gpsWaitFix(5000))
  {
GPSLat = gps.location.lat();
GPSLon = gps.location.lng();
GPSAlt = gps.altitude.meters();
GPSSats = gps.satellites.value();
displayInfo();

  }
  else
  {
Serial.print(F("Timeout - No GPS Fix "));
Serial.print( (millis() - startGetFixmS) / 1000 );
Serial.println(F("s"));
  }
  Serial.println();
}


bool gpsWaitFix(uint32_t waitmS)
{
  //waits a specified number of millis for a fix, returns true for updated fix

  uint32_t startmS;
  uint8_t GPSchar;

  Serial.print(F("Wait GPS Fix "));
  Serial.print(waitmS);
  Serial.println(F("mS"));

  startmS = millis();

  while ( (uint32_t) (millis() - startmS) < waitmS)   //allows for millis() overflow
  {
if (GPSserial.available() > 0)
{
  GPSchar = GPSserial.read();
  gps.encode(GPSchar);
  Serial.write(GPSchar);
}

if (gps.speed.isUpdated() && gps.satellites.isUpdated()) //ensures that GGA and RMC sentences have been received
{
  endFixmS = millis();   //record the time when we got a GPS fix
  Serial.println();
  Serial.print(F("Fix time "));
  Serial.print(endFixmS - startmS);
  Serial.println(F("mS"));
  return true;
}
  }
  return false;
}


void displayInfo()
{

  Serial.print(F("New GPS Fix "));
  Serial.print(F("Lat,"));
  Serial.print(GPSLat, 6);
  Serial.print(F(",Lon,"));
  Serial.print(GPSLon, 6);
  Serial.print(F(",Alt,"));
  Serial.print(GPSAlt, 1);
  Serial.print(F("m,Sats,"));
  Serial.print(GPSSats);
  Serial.println();
}


void setup()
{
  GPSserial.begin(9600);
  Serial.begin(115200);
  Serial.println();
  Serial.println(F("Robust_GPS_reading_with_SoftwareSerial Starting"));
  Serial.println();

  startGetFixmS = millis();
}



#### Notes about isValid()

[https://github.com/mikalhart/TinyGPSPlus/issues/12](https://github.com/mikalhart/TinyGPSPlus/issues/12)

isValid() simply means that the location information contains valid (though possibly stale) data. isValid() returns FALSE until the very first NMEA sentence with a fix is parsed. From then on it returns TRUE forever.

   

### Stuart Robinson

### September 2024




