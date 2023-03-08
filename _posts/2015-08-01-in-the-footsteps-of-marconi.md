---
layout: post
title: "In the Footsteps of Marconi"
date: "2015-08-01"
---

## The Benefits of Height in UHF Communications

The environment, what is between transmitter and receiver has a very significant affect on UHF communication. In an urban area the buildings between the transmitter and receiver will severely attenuate the UHF signals. Its much the same for wooded areas such as forests, trees severely attenuate signals. Lets assume the limit of reception between transmitter and receiver at ground level in an urban area is 1km. With good line of sight between transmitter and receiver, such as from the ground to a high altitude balloon or a satellite then the range could be up to 1000km. The difference between and obstructed view and good line of sight really can be as much as 1000:1 for UHF communication. 
At ground level, in an urban area or forest, then your not going to get good line of sight (LOS).  However, if your receiver were directly above the transmitter, there would be good LOS and thus reception range is dramatically increased. 

So for a transmitter on the ground, the higher we can get the receiver then the better will be the LOS view, and thus the longer the range. 

For a transmitter in an urban area its easy enough to demonstrate the effect with a long pole. I carry a 10M extensible pole in my car, its useful for persuading model aeroplanes out of trees, its fairly small when packed, only 66cm long, see picture. 


<br>
<p align="center">
  <img width="450"  src="/images/Image1.jpg">
</p>

<br>

With the transmitter on a table in my garden I wandered around the locality with the receiver on the end of the pole. I had it set up to measure the effect in signal strength with the receiver at shoulder height and on the top of the pole. With the receiver on top of the pole, signal strength improved by at least 6dB, and by 12dB if the pole allowed the receiver to be above obstructing trees or buildings. 

These signal improvements represent a distance improvement of 2 and 4 respectively. Thus a simple pole could increase the search range by at least 2, possibly more. 

With practical fixed installations the effect of height is well known, UHF antennas are often placed on masts or the roof of buildings in order to improve range. Whilst higher is better in the context of using LoRa for hearing (and locating) a tracker transmitter, there is a limit as to the height of mast we can carry around with us. Spiderbeam make an 18M fibreglass collapsible mast, but that is about the limit of what can be easily erected. 

On way to get height above the transmitter is to use geography. For instance 4.5km to the North of my garden is a hill overlooking the city, from here a transmitter on the ground in the garden can easily be picked up with a hand held receiver. 
However, they wont always be a convenient hill overlooking the search area. 

<br>
<p align="center">
  <img width="450"  src="/images/Image2.jpg">
</p>
<br>


To do some more practical testing, one day I placed a test transmitter on a pole in my 
garden.  

<br>
<p align="center">
  <img width="450"  src="/images/Image3.jpg">
</p>
<br>


Fitted my magnetic mount antenna to the roof of my car and went for a drive.

<br>
<p align="center">
  <img width="450"  src="/images/Image4.jpg">
</p>
<br>

On the edge of Cardiff Bay and 7km away from the transmitter in the North of Cardiff, I could pick up the signals with the car magnetic mount antenna but not with the hand held receiver and its small antenna. However with a portable yagi, I was able to pick up the signals even though the view back to the transmitter was significantly obstructed by buildings, see picture below; 

 
<br>
<p align="center">
  <img width="450"  src="/images/Image6.jpg">
</p>
<br>

A lot of trackers use a fairly bulky receiver, either a portable UHF transceiver or custom made receivers as used by the wildlife trackers. Apart from using a handy nearby mountain, one way with these types of tracker to get a good line of sight above a tracker ‘lost’ on the ground is to hire a person carrying aeroplane or helicopter, perhaps not too practical.  

The Arduino Pro Mini LoRa tracker I built is small and light and by its nature specifically designed to be carried in (small) model aeroplanes or copters. The tracker transmitter is a receiver as well and you can write code for it to act as a telemetry relay. In this mode the tracker (on the plane or copter) listens for incoming telemetry and then re-transmits the packets down to the ground. Range from the plane to the ground is no issue, since the plane is not that far away and you have good line of sight to it. 

So I taped my relay tracker to a small easy build and low cost plane, an AXN Floater, see below.

<br>
<p align="center">
  <img width="450"  src="/images/Image7.jpg">
</p>
<br>


In 1897 Marconi used the height of the cliffs at Lavernock point to demonstrate the first radio communications over water. 
Over a hundred years later I went to the same spot on the coast, see picture below, and 12km from the tracker transmitter in my garden. 


<br>
<p align="center">
  <img width="450"  src="/images/Image8.jpg">
</p>
<br>

Would I be able to pick up the transmitter from this spot?

I launched and flew the plane up to altitude. In a few seconds and at around 100M up 
the relay had picked up the telemetry from the transmitter 12km away and relayed it 
back down to my receiver, success! 

The potential here is clear, with a simple Radio Controlled plane or copter and the 
right software you can dramatically increase the search range. Flying higher and 
fitting a better antenna on the search plane should increase the range even further. 
The LoRa devices used in the tests above were Hope RFM98s, running at 10mW. The
device is capable of 50mW, which if used should double the range.



## Worst case?

In the previous tests the ‘lost’ model transmitter was at chest height. What would happen in a worst case scenario when the ‘lost’ model is on the ground surrounded by buildings? There is now no clear view of the horizon, so picking up transmissions from the ‘lost’ model locator is going to be difficult. The height you can take the LoRa repeater to with an RC model plane is limited to around 500M or so which may not be enough for our worst case situation. This is especially true if the potential search area is 10s of km in diameter. 

Fortunately there is a very simple and low cost solution to getting our LoRa repeater to a much higher altitude and thus significantly increasing the search range. 

A 36” foil party balloon filled with helium will easily carry a small GPS tracker (and LoRa repeater) to high altitude. You can fill the balloon with just enough helium so that it stops rising at around 8000M and continues to float in the wind at this altitude. Alternatively if you add a bit more helium the balloon will rise till it bursts. Since its a GPS tracker you can then follow it down and retrieve it on landing. 

There is a high altitude balloon tracking network in the UK, where other enthusiasts pick up your signals and forward them across the Internet to a central tracking server, but this type of tracking network is not really needed with LoRa.

Using LoRa with even a modest gain vertical omni directional antenna you will be able to track a balloon direct to around 250kM distant when its using a mere 10mW of transmit power. A small yagi on a tripod should go even further. The ground based receiver can be equipped with its own GPS and can thus calculate exactly where to point the yagi. This type of self tracking uses minimal equipment and would be useful in areas where there are no other listening stations. Its also straight forward to have a portable computer display the location and track of the balloon on a mapping application.

A foil party balloon (see picture below) will cost around £4 and have maybe the same amount of helium in it, so assuming you get the balloon tracker back it can be a cost effective way of searching for an expensive ‘lost’ model. The tracker is around £20 in parts. 


<br>
<p align="center">
  <img width="450"  src="/images/Image9.jpg">
</p>
<br>


To test the theory I set up one of my balloon trackers (see picture below) to listen for the signals coming from the ‘lost’ model and relay its GPS location back down to the ground. The ‘lost’ model transmitter was placed on the grass in my garden in the middle of a city. 


<br>
<p align="center">
  <img width="450"  src="/images/Image10.jpg">
</p>
<br>


I went to a nearby hillside and launched the foil balloon and its tracker. The ‘lost’ model was first located when the balloon was around 150M in the air and the ‘lost’ model was around 6Km away. 
The ‘lost’ model continued to be detected till the balloon (with tracker relay) was 26km away and was at a height of 3364M. This is a search area of 52km diameter. I lost two way contact with the tracker itself at around 240kM. 

Using a LoRa tracker\repeater on a foil party balloon (or long distance model plane) has potential for collecting data from widely spaced ground based transmitters where there is no possibility of telephone or Internet based connectivity back to base.

### Stuart Robinson 
### GW7HPW
### October 2015









