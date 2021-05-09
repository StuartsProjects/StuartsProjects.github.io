---
layout: post
title: "GPS Performance Comparisons"
date: "2019-02-01"
---

# GPS Tracker Performance 

The amount of current the GPS uses in a tracker application is an often overlooked issue. The designer may spend a lot of time concentrating on designing a GPS tracker project with an extremely low sleep current without appreciating the amount of power the GPS uses when it is running, acquiring a fix and when its in backup mode.

If a GPS in 'hot fix' mode consumes 50mA (a typical figure) and takes an average of 5 seconds to get a new fix, and you do that every 10 minutes, then in one day it will consume;

50 x 5 x 6 x 24 = 36000maSecs 

or

10mAhr per day. 

Now 10mAhr over a day may not seem a lot, but if the batteries were a pack of AAs then battery life would be less than 280 days.

10mAhr a day is an average current of 416uA, so it seems rather pointless worrying whether a project consumes 10uA in sleep versus striving for 0.1uA or even less. 

Do note that whilst a 'hot fix' of a few seconds may be quoted as the norm, the average 'hot fix' time over a long period will often be 10 seconds or more, as the tests in this article show. 


### GPS Signal Performance

For a range of GPSs I will first measure the signal receiving performance of the GPS. For this we will use the UBLOX U-Center application which when connected to a GPS produces a chart of satellites received and their signal strengths. GPSs with a good signal performance will achieve initial lock quicker, work better in more marginal conditions and will overall require less power to operate. 

U-Center uses the NMEA GSV sentence to obtain the signal strengths of the satellites;

*Signal strength, in dB-Hz (0 - 99)
To compare with SNR values found in Bin messages (such as Bin96) subtract 30 from this signal strength value for an approximate SNR value
SS - 30 = SNR (from Bin message)*

A signal level of 26 or lower is highly marginal and you may not get a fix, or it could take a very long time. A value of 40 or more is a strong signal. If a GPS is receiving 3 or more satellites at this signal level or greater then it can be expected to acquire a fix in 30-50 seconds from cold.

The U-Center application is designed for UBLOX GPSs but it will display satellite information for other GPS makes also. 

In the roof of my workshop (Shed!) I have a clear plastic panel and I placed all the GPSs in turn underneath this on a high shelf. The location was not ideal for good GPS reception but indicative of a slightly marginal situation. 

The GPSs were connected to U-Center in turn and time was recorded for the GPS to acquire its first fix and the current consumption was also measured. 

Modern GPSs should when connected to a decent working antenna get a fix from cold in 30 to 60 seconds. If it takes longer either the GPS or its antenna are faulty or the GPS has a poor view of the sky. You may often see it commented that GPSs can take 15 minutes or even longer to acquire a fix from cold. I assume this in an urban myth maybe based on ancient history. All the GPSs I have tested in recent years stick to the 30 to 60 second rule, unless they are faulty. 


### GPS Hot Fix Performance

A 'hot fix' takes place when a GPS is powered on or woken up from a software or hardware based sleep mode. If the GPS has a memory backup then it will use the previously downloaded GPS satellite information stored in its memory to quickly establish a new location fix. If there has been little movement across the sky in the GPS satellites that are visible since the last fix, this 'hot fix' can take as little as two seconds. Clearly if the GPS only needs to be on for two seconds this can result in significant power savings. However GPS satellites do move out of view and new ones appear so that over time the GPS will need to download information on new satellites which takes time. Thus whilst initially 'hot fix' times will be very short over a period of hours there will be longer 'hot fix' times, as you will see in the graphs below. 

The UBLOX GPSs can be shut down to around 15uA in backup mode via software alone, which is handy. The Mediateks and Quectel GPSs can also be shut down via software but the backup current only reduces to 1mA or so. 

The only practical way to monitor the actual current consumption of a GPS tracker over a long period is to externally measure the actual mAhr used, if the hot fix time varies a lot its going to be difficult to calculate the figure. As an example it was noted that for one of the UBLOX GPSs used in these tests the hot fix time varied from 2.0 seconds to 32 seconds, its thus very difficult to come up with an average for calculation purposes.   

Some Arduino software was written that powered up the tracker and GPS, recorded how long the fix took, powered down the GPS via software, sent the GPS information at bandwidth 125000Hz and SF12 (worse case for long distance) and transmitted the location packet. The tracker was then powered off completely by a real time clock based **power controller** (DS3231) of my own design;

![Picture 1](/images/DS3231PowerController.jpg)
<br><br>

The power controller goes in the battery lead of a project and is controlled from the project with a 4 wire I2C lead. Power off time is programmable from seconds to one month. For this GPS test it turned off the tracker for 10 minutes, so this was the GPS location transmission interval. The sleep current for the entire arrangement was 1.5uA. 

A large lead acid gel **battery** feeding a 5V output step down **power converter** was connected to a **USB power meter** which would record the total mAhr being used by the tracker.  

![Picture 1](/images/101.jpg)

The entire package was placed outdoors in my garden on a table which would be around the height of a tracker when worn by a small animal. 

![Picture 1](/images/100.JPG)

The Arduino receiver software was written for the Pro Mini based LCD receiver of my own design. It recorded the received packets, including GPS fix time, and reported them to an attached display, serial monitor and micro SD card log. The SD card log format was in CSV so it was straight forward to read to data on the file into a spreadsheet and draw a graph of the variation in fix times. 


<br><br>

### GPS Performance Tests - Report Format

The GPSs were initially tested for signal performance and fix time in 3 batches. Below the testing of each batch is reported starting with a picture of the GPSs. The GPS is named and the information presented in this order, Fix time from cold, current consumption whilst acquiring fix, current consumption after fix, total tracker power used in 24 hours, GPS mAhr used in 24 hours, battery life for a pack of AA Alkalines. To calculate the power just used by the GPS the consumed by the tracker with no GPS was measured. The total up time was 2.91 seconds with a power consumption of 7.2mA when idle and 44mA when transmitting. The transmit time was 0.925 seconds, this equates to 0.0917 mAhr per hour or 2.2mAhr per day. With that figure known it was possible to subtract the figure from the total measured mAhr when the transmissions and GPS were in use in order to isolate the GPS only figure.     

The GPS signal graph is then shown for the GPS, note that green bars mean the satellite is available for fix information blue means it is not.  At the bottom of the bar is the signal strength, and below that the satellite number. 

If the 24 hour hot fix test has been run that is presented next. This graph shows the variation in hot fix times over a 24 hour period. The fixes are every 10 minutes. 
 

### GPS Tests


This is the first set of GPSs';


![Picture 1](/images/GPS1A.jpg)

And the respective plots from U-center were are shown below, GPS 3 is a UBLOX6M tested with the large ceramic patch antenna (3A) and the smaller one which is sometimes used (3B).

<br><br>



#### 1 - UBLOX 8M GPS
Often used on quad copters and similar.
<br><br>

* With 25mm ceramic patch antenna. 
* Fix time from cold, 46 seconds.
* Current whilst acquiring fix, 34mA to 50mA.
* Current after fix acquired, 38mA to 50mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 21.5mAhr
* GPS Power in 24 hours, 10 minute fixes, 19.3mAhr
* Tracker battery life AA Alkalines 133 days

![Picture 1](/images/1.jpg)

![Picture 1](/images/1F.jpg)

<br><br>

#### 2 - UBLOX 7 GPS - Ceramic Patch
* With 25mm ceramic patch antenna. 
* Fix time from cold, 35 seconds.
* Current whilst acquiring fix, 50mA to 54mA.
* Current after fix acquired, 45mA to 56mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 38.1mAhr
* GPS Power in 24 hours, 10 minute fixes, 35.9mAhr
* Tracker battery life AA Alkalines 73 days

![Picture 1](/images/2.jpg)

<br><br>

![Picture 1](/images/2F.jpg)
<br><br>
<br><br>


#### 3A - UBLOX 6M GPS
* With external plug in 25mm ceramic patch antenna.
* Fix time from cold, 35 seconds.
* Current whilst acquiring fix, 50mA to 55mA
* Current after fix acquired, 45mA to 55mA
* Total Tracker Power in 24 hours, 10 minute fixes, 80.7mAhr
* GPS Power in 24 hours, 10 minute fixes, 78.5mAhr 
* Tracker battery life AA Alkalines 35 days
<br><br>


![Picture 1](/images/3A.jpg)
<br><br>

![Picture 1](/images/3AF.jpg)


<br><br>
#### 3B - UBLOX 6M GPS
* With plug in  mini ceramic patch antenna. Much reduced signals. No fix.

![Picture 1](/images/3B.jpg)

<br><br>
#### 4 - unknown type.
* With 18mm ceramic patch antenna.
* Fix time from cold, 25 seconds.
* Current whilst acquiring fix, 38mA to 53mA
* Current after fix acquired, 38mA to 53mA

![Picture 1](/images/4.jpg)

<br><br>

### High Altitude Balloon GPSs

**GPSs 5,6 and 7** below are typical types for high altitude balloon (HAB) tracking, all 3 have a high altitude mode allowing operation above 18,000M

At the end of the report there are test results for the Quectel L76, this GPS is similar to the L70 but with the addition of multi GNSS support for GPS, GLONASS, BeiDou, Galileo and QZSS.


![Picture 1](/images/GPS2A.jpg)


<br><br>
<br><br>
#### 5H Quectel L70
* With simple horizontal wire antenna.
* Fix time from cold, 32 seconds.
* Current whilst acquiring fix, 14mA to 15mA
* Current after fix acquired, 15mA to 22mA. The L70 is the same size and pin layout as the UBLOX MAXM8Q.
* Total Tracker Power in 24 hours, 10 minute fixes, 17.6mAhr
* GPS Power in 24 hours, 10 minute fixes, 15.4mAhr 
* Tracker battery life AA Alkalines 159 days

<br><br>
![Picture 1](/images/5H.jpg)
<br><br>
![Picture 1](/images/5HF.jpg)
<br><br>

#### 5V Quectel L70
* With simple vertical wire antenna. 
* Fix time from cold, 36 seconds.
* Current whilst acquiring fix, 14mA to 15mA
* Current after fix acquired, 15mA to 22mA

![Picture 1](/images/5V.jpg)

<br><br>

#### 6H UBLOX MAXM8Q
The standard high altitude balloon (HAB) GPS perhaps.

* With simple horizontal wire.
* Fix time from cold, 5 minutes 46 seconds.
* Current whilst acquiring fix, 22mA to 28mA
* Current after fix acquired, 20mA to 25mA
<br><br>

Breakout board had no connection to Vbackup pin, so could not test hot fix. 
A second example of the MAXM8Q with horizontal wire antenna was tested, Fix time from cold, 7 minutes 35 seconds.

![Picture 1](/images/6H.jpg)
<br><br>
<br><br>
#### 6V UBLOX MAXM8Q
* With simple vertical wire.
* **No fix acquired in 20 mins**. 
* Current whilst acquiring fix, 22mA to 28mA

![Picture 1](/images/6V.jpg)
<br><br>


<br><br>
#### 7 Quectel L70 
* With 25mm ceramic patch on PCB rear.  
* Fix time from cold, 30 seconds.
* Current whilst acquiring fix, 14mA to 15mA
* Current after fix acquired, **11.2 to 14.6mA**
* Total Tracker Power in 24 hours, 10 minute fixes, 15.76mAhr
* GPS Power in 24 hours, 10 minute fixes, 13.56mAhr 
* Tracker battery life AA Alkalines 178 days


![Picture 1](/images/7.jpg)

<br><br>


![Picture 1](/images/7F.jpg)

<br><br>
<br><br>

The next 4 GPS tested are pictured below, No8 is a Quectel L80 module.


![Picture 1](/images/GPS3A.jpg)
<br><br>
<br><br>
####  8 - Quectel L80 

* With integral 15mm ceramic patch.  
* Fix time from cold, 30 seconds.
* Current whilst acquiring fix, 21.6mA to 28mA
* Current after fix acquired, 21mA. 
* Total Tracker Power in 24 hours, 10 minute fixes, 19.72mAhr
* GPS Power in 24 hours, 10 minute fixes, 17.52mAhr 
* Tracker battery life AA Alkalines 142 days


![Picture 1](/images/8.jpg)
<br><br>

![Picture 1](/images/8F.jpg)

<br><br>
<br><br>

###  9 - GlobalTop PA6H 

* Fix time from cold, 38 seconds.
* Current whilst acquiring fix, 22mA to 23mA.
* Current after fix acquired, 18mA to 25mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 25.8mAhr
* GPS Power in 24 hours, 10 minute fixes, 23.6mAhr 
* Tracker battery life AA Alkalines 109 days

![Picture 1](/images/9.jpg)

<br><br>

![Picture 1](/images/9F.jpg)

<br><br>

####  10 - Beitian BN220 

* Fix time from cold, 38 seconds.
* Current whilst acquiring fix, 47mA to 57mA.
* Current after fix acquired, 47mA to 53mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 80.4mAhr
* GPS Power in 24 hours, 10 minute fixes, 78.2mAhr 
* Tracker battery life AA Alkalines 35 days


![Picture 1](/images/10.jpg)

<br><br>

![Picture 1](/images/10F.jpg)

<br><br>


####  11 - UBLOX NEO 6M with Ceramic Patch 

* Fix time from cold, 122 seconds.
* Current whilst acquiring fix, 46mA to 56mA.
* Current after fix acquired, 45mA to 57mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 72.2mAhr
* GPS Power in 24 hours, 10 minute fixes, 70mAhr 
* Tracker battery life AA Alkalines 40 days


![Picture 1](/images/11.jpg)

<br><br>

![Picture 1](/images/11F.jpg)

<br><br>


The next GPSs to be tested as and when they arrive, I have ordered a UBLOX SAM M8Q, Quectel L76 and Quectel L86. The L76 is in effect an L70 with added  LNA. Whilst the L70 manages well enough without the LNA, the addition ought to improve the hot fix times, reducing in turn the power consumption. The L86 is a GPS with an integral ceramic patch similar in specification to the UBLOX SAM M8Q in that is supports GPS, GLONASS, Galileo and high altitude mode. 


![Picture 1](/images/GPS4A.jpg)


####  12 - UBLOX SAM M8Q with Ceramic Patch 

* Fix time from cold, 36 seconds
* Current whilst acquiring fix, 26mA to 28mA.
* Current after fix acquired, 26.5mA to 30mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 14.32mAhr
* GPS Power in 24 hours, 10 minute fixes, 12.12mAhr 
* Tracker battery life AA Alkalines 195 days

![Picture 1](/images/12.jpg)

<br><br>

![Picture 1](/images/12F.jpg)
<br><br>

####  13 - Quectel L76 with horizontal wire antenna

The Quectel L76 is a GPS similar to the L70 but with the addition of multi GNSS support for GPS, GLONASS, BeiDou, Galileo and QZSS.

* Fix time from cold, 35 seconds
* Current whilst acquiring fix, 26mA to 40mA.
* Current after fix acquired, 19mA to 33mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 20.9mAhr
* GPS Power in 24 hours, 10 minute fixes, 18.7mAhr 
* Tracker battery life AA Alkalines 133 days

![Picture 1](/images/13.jpg)



<br><br>

![Picture 1](/images/13F.jpg)
<br><br>
####  13 - Quectel L76 - with horizontal wire antenna - 60 second shutdown. 


To test the performance of the L76 that might be typical of a high altitude balloon tracker, I used my DS3231 power controller to shut the tracker down every 60 seconds. The tracker was waking up, waiting for a fix and then transmitting the location with LoRa at a bandwidth of 125Khz and spreading factor of 12. This is approximately 300bps. 

In 24 hours the tracker consumed 145mAh of battery.

The spread of fixes is shown below. 


![Picture 1](/images/13F2.jpg)


<br><br>


####  14 - Quectel L86 with ceramic patch antenna

The Quectel L86 is a GPS similar to the L80 but with the addition of multi GNSS support for GPS, GLONASS, BeiDou, Galileo and QZSS.

* Fix time from cold, 48 seconds
* Current whilst acquiring fix, 30mA to 44mA.
* Current after fix acquired, 23mA to 37mA.
* Total Tracker Power in 24 hours, 10 minute fixes, 27.7mAhr
* GPS Power in 24 hours, 10 minute fixes, 25.2mAhr 
* Tracker battery life AA Alkalines 102 days

![Picture 1](/images/14.jpg)


<br><br>

![Picture 1](/images/14F.jpg)


<br><br>


####  Quectel L76 with Wire versus Ceramic patch antenna

The Quectel L76 has a built in LNA and so should cope well with a simple wire antenna or a small ceramic patch antenna. The fix performance with a wire antenna was tested earlier, but below is the signal performance of the GPS first with a wire antenna (left) and a small 15mm ceramic patch antenna. The ceramic patch antennas are stick on an easy to fix in place on the rear side to the GPS. The Wire antenna provides a bit more signal gain but at 4.75cm long may not be suitable in some applications. The ceramic patch is much more compact yet still give adequate performance.


![alt-text-1](/images/13_wire.jpg "Wire Antenna") ![alt-text-2](/images/13_patch.jpg "patch Antenna")

<br><br>

### GPS Performance Tests - Summary 

The best performer was the UBLOX SAM M8Q with a battery life of 195 days on AA Alkalines. Its not a low cost GPS however, £18 upwards. 

In the performance tests the Quectel GPSs all did well considering their  very low cost. They had substantially lower power consumption than similar or higher priced UBLOX GPSs.

The Quectel L80 is physically a similar GPS to the SAM M8Q, has a very good performance and at £3.50 is much cheaper than the UBLOX. Its also in a package that makes soldering to a PCB easy. The Quectel L80 perhaps the best compromise GPS.

The bare bones L70 module (i.e. it comes with no antenna) is worthy of note, it's the same size and pin layout as the equivalent UBLOX MAXM8Q module but has a far better signal performance and a very low current consumption. The L70 has a high altitude balloon mode, and is easy to find as a bare module. I recently bought 5 off for £3.50 each delivered. Under the same signal conditions as the UBLOX MAX M8Q the L70 with a simple wire antenna behaved like a standard GPS with ceramic patch antenna; a cold fix time of 32 seconds whilst the UBLOX MAX M8Q took 5 minutes or more. 

The Quectel L76 and L86 are pin compatible with the L70 and L80 respectively and whilst they add multi GNSS capability they are slightly more expensive and use more power. Both these GPSs have a high altitude balloon mode.   

It should be noted that over a long period hot fix times are in the 10 second average range for most GPSs, this is far more than the often quoted headline figures of 2 - 5 seconds. It can be seen from the graphs that there are occasional fixes that take 30 seconds or more. These long fix times are required to download ephemeris data from new satellites that come into view. The detail of this data is constantly changing so you cannot store it long term.  

It also need to be appreciated just how much power a GPS based project actually uses. Even for the low power UBLOX SAM8Q and Quectels, the GPS was consuming more than 85% of the projects power, the rest being used for the LoRa transmissions. 

With the GPS consuming so much of a projects current it is clear that the trackers sleep current is of very little consequence, even at a 'high' level of 5uA, which is easy to achieve with standard Arduino parts, the sleep current is only 1% of the total project current.   

This report notes the importance of having access to the Vbackup pin on the GPS for long term tracker use. There is one GPS module available that provides access to this pin, the [Adafruit Ultimate GPS](https://www.adafruit.com/product/746). I have not tested this GPS, cost is £37, but it should have a similar performance to no. 9 in this report.


<br><br>



## Stuart Robinson
## February 2019
 