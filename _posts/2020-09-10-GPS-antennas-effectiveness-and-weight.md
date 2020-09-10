---
layout: post
title: "GPSs and antennas; effectiveness and weight."
---

Antennas for GPSs come in a variety of forms, for some the antenna is built in or part of the GPS module and on others you add a separate antenna. 

For some applications, such as high altitude balloon trackers a low weight is important, but is less important for general purpose GPS applications. 

This first part of a report on the effectiveness of GPSs and antennas will concentrate on the type of GPS that would be used in high altitude balloon trackers, the second part will look at general purpose GPSs. 

GPSs for high altitude balloon trackers need to be put into ‘high altitude’ or ‘balloon mode’ otherwise they stop providing a fix at typically above 10,000m. In this balloon mode once the GPS is above this it will continue to provide a fix as long as the speed the GPS and its tracker are travelling at is low. These restrictions are in place to prevent readily available GPSs being used in missiles etc.

A popular GPS for high altitude balloon use has been the Ublox MAX M8Q, which together with a small ceramic chip antenna is a fairly light (1.7g) and compact setup on a breakout board. I have never been happy with the performance of this setup, first fix times from cold on the ground can take many minutes. Performance in the air where the tracker has a good view of satellites is generally OK. However, if you need to find a balloon that has landed from its continuing tracker position fixes, then you do need a GPS that performs well on the ground or when surrounded by trees.

The GPSs and antennas used in this post are pictured below, left to right,  Ublox MAX M8Q with ceramic chip antenna,  Ublox MAX M8Q with wire antenna, Quectel L70 with ceramic chip antenna,  Quectel L70 with wire antenna,  Quectel L70 with ceramic patch antenna (front), Quectel L70 with ceramic patch antenna (rear),  Quectel L80 with ceramic patch antenna and Quectel L86 with ceramic patch antenna. 

A good measure of how effective a GPS antenna is how long it takes to provide the first fix from cold and this is fairly straight forward to measure. If a GPS or its antenna is poor then there is more probability of the satellite signals being at or near the signal C\No threshold which in turn increases the chance of bit errors when downloading the necessary data from a particular satellite. The greater the bit errors the longer it will take a GPS to have downloaded the correct data it needs from the satellite in order to compute a location. 

So in approximate terms, a good GPS with a good antenna will have a shorter first fix time than a poor GPS with a poor antenna. 

A GPS puts out the numbers of the satellites it has in view in the GPGSV NMEA sentence, this data also includes the signal strengths being seen and an application such a the Ublox Ucenter can read the satellite signal strength data and display it as a bar chart. Thus you get a visual indication of how strong are the satellite signals that the GPS is actually seeing. With antennas, as with a lot of things, size matters and you would expect a small 7×3mm chip antenna to not perform as well as a much heavier 25mm square ceramic patch.  

I set up an application so that I could connect a GPS to an Arduino Pro Mini and have it record the time taken for the GPS to get a fix from power being applied together with the satellites in view number taken from the GPGSV sentence. This information was sent wirelessly to a receiver so with the various GPSs under test and in a consistent location the fix times could be monitored. 

The location for the GPS was on a table in my garden, I live in an urban area so the houses around would likely obscure some of the view the GPS had of the satellites and this would be expected to extend fix times. The typical balloon launch site would very likely allow the GPS a much better view of the sky and thus have reduced cold fix times, but the urban test location is perhaps more representative of the conditions a landed balloon might experience. 

The tests were carried out in a short period of time, one GPS at a time, so that on average the GPS saw similar GPS satellite positions in the sky. The results are tabulated below; 

#### **Summary**.
As with previous experiences the small ceramic chip antenna was a (very) poor performer, although it is a light and compact setup, 1.7g for a Ublox MAX M8Q on a breakout and 2.1g for a Quectel L70, although the M8Q breakout PCB was smaller than the L70. 

The simple wire antenna, whilst around the same weight and the ceramic chip antenna setup was a major improvement, with short and reliable cold fix times in what was far from an ideal location.  

Below are comments on each type of GPS\Antenna combination together with a screen shot of the Ucenter output indicating the satellite signal strengths being seen by the GPS.

<img align="right" src="/images/13a.jpg" width="250" height="250">
##### **Ublox MAX M8Q with ceramic chip antenna (1.7g)**



This setup did on one occasion get a fix, in 686 seconds, but mostly it just picked up one satellite. It had 2 in view 4 times (the reports were 10 seconds apart) and once it had 3 in view. This was over a 40 minute period with no fix in sight. 

##### **Quectel L70 with ceramic chip antenna (2.11g)**
Performed a lot better than the Ublox MAX M8Q, in that it did actually get a fix but average fix time was still very poor at 434 seconds. 

##### **Ublox MAX M8Q (1.75g) with a simple ¼ wave wire** 
This setup had and average cold fix of 60 seconds, although two other examples had averages of 101 seconds and 249 seconds. So for this GPS a simple wire antenna is a major improvement over a ceramic chip. There is quite a difference between 60 seconds and 249 seconds, and it is possible the particular Ublox  MAX M8Q was faulty, they are known to be sensitive to static damage on the antenna input, something the Quectel L70s do not seem to suffer from. 

##### **Quectel L70  (2.09g) with a simple ¼ wave wire**
Average cold fix time of 40 seconds.

##### **Quectel L70  (6.04g) with a small ceramic patch antenna**
Average cold fix time of 35 seconds. 

##### **Quectel L80 (7.44g) with built in ceramic patch antenna**
This would would get a fix from cold in 40 seconds.

##### **Quectel L86 (9.54g) with built in ceramic patch antenna**
Would get a fix in 40 seconds, but if the ‘cold fix’ test was carried out again within a few minutes then the fix time was around 15 seconds. This GPS is storing downloaded satellite data internally, although how long this backup lasts, I do not know.


| Type  | Antenna | Weight g | Average secs |
|-|-|-|-|
| Ublox M8Q | Ceramic chip | 2.09 | 686 |
| Ublox M8Q | Wire | 1.75 | 101 |
| Ublox M8Q | Wire | 1.75 | 249 |
| Ublox M8Q | Wire | 1.75 | 60 |
|  |  |  |  |
| Quectel L70 | Ceramic chip | 2.11 | 434 |
| Quectel L70 | Wire | 2.09 | 41 |
| Quectel L70 | Wire | 2.09 | 39 |
| Quectel L70 | Ceramic Patch | 6.04 | 36 |
| Quectel L70 | Ceramic Patch | 6.04 | 33 |
| Quectel L70 | Ceramic Patch | 6.04 | 35 |
| Quectel L76 | Ceramic Patch | 6.04 | 31 |
| Quectel L80 | Ceramic Patch | 6.04 | 38 |
| Quectel L86 | Ceramic Patch | 9.54 | 21 |



### **Stuart Robinson**
### **September 2020**     