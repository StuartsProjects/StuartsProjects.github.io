---
layout: post
title: "Finding Lost Trackers"
date: "2016-06-23"
---

Once a balloon tracker has landed it will normally continue to send its GPS derived position. With your radio gear you can decode the FSK RTTY to a laptop or phone application. With a LoRa based tracker you can use a small hand held receiver which will tell you where the tracker is and how far and in what direction you need to travel to find it.

But what do you do to find your tracker if after landing the GPS has no reception or it has failed for some reason, or maybe its just not convenient to carry a rucsac of radio gear to pick up a FSK RTTY only tracker.

One possible answer is to use basic radio direction finding (RDF) techniques by listening for the trackers FSK RTTY transmissions. A UHF Frequency Modulation (FM) hand held such as the cheap Baofengs will detect these FSK signals but you don’t need to decode them as such, just listen to the audio.

If you turn off the squelch on the hand held so that you hear continuous background noise, you will notice that the background noise goes away when FSK RTTY is received. The FM hand held sees the signal, but there is no real FM content for it to decode so you ‘hear’ silence. The stronger the received signal, the quieter it sounds.

This quieting caused by the FSK RTTY is noticeable even when the tracker is a considerable distance away and received signals are very weak. With a directional antenna such as a Yagi or simple Moxon, you can get a good bearing on the tracker by moving the antenna around for minimum noise, when noise is at a minimum the antenna is pointing at the tracker.

**Directional Antennas**

Examples of directional antennas are shown below, the first is an Arrow yagi, this is robust, collapses well for transport and is quick to assemble.

![Arrow](/images/Arrow_thumb.jpg "Arrow")]

![Arrow - Packed](/images/Arrow-Packed_thumb.jpg "Arrow - Packed")

Below is a ZL Special, this has around the same gain as the Arrow but is a lot shorter which can be a useful attribute when your fighting dense vegetation.

![ZL Special](/images/ZL-Special_thumb.jpg "ZL Special")


You don’t have to buy ready made antennas, there are plenty of plans to be found on the Internet for DIY builds for the UHF 434Mhz band as this is very close to Amateur radio allocations on 70cm.

The Moxon is a very handy antenna to use for RDFing, its easy to build and packs flat, mine is made of a bit of plastic, general build instructions can be found here;.

[http://www.moxonantennaproject.com/sm5jab/sm5jab\_2.htm](http://www.moxonantennaproject.com/sm5jab/sm5jab_2.htm)

![Moxon](/images/Moxon_thumb.jpg "Moxon")


The Moxon does not have as much gain as a long yagi, but that can be a good thing.

Getting a good bearing with a gain directional antenna is straight forward when the tracker is 1km or more distant and the signals are weak. You just wave the antenna around for the lowest noise level, the antenna is then pointing in the direction of strongest signal and you can head off towards it.

**The problem with gain directional antennas.**

It wont be long before you discover that such directional antennas have far too much gain to give a reliable bearing when you are close to the tracker, say 250M or less. The real problem to solve is how to use RDF techniques when the tracker is close and the signals are strong.

Take a look at the picture, this is a local park which I have used a lot over the years to test various locating methods. If you put a FSK RTTY tracker transmitter (10mW) in the middle of the woods then on the North West edge of the parks perimeter which is 250M away the Arrow yagi is of little use, the signals are far too strong to get any reasonable idea as to direction and its the same with the ZL Special.

![park](/images/park_thumb.jpg "park")


In theory gain antennas as shown here have nulls in the radiation pattern which it is often suggested can be utilised for RDF by pointing the antenna for weakest signal (=maximum noise). In the real world this often does not work, and not with my Arrow or ZL Special antennas. The Arrow does exhibits a weak null at about 45 degrees to straight ahead but its not uniform on either side. Pointing either antenna backwards makes little difference, nor does rotating the antenna along its long axis to change the polarization.

So the challenge is to reduce the signal that the hand held sees in some way. One way is to use in line attenuators, such as those shown here.

![Attenuators](/images/Attenuators_thumb.jpg "Attenuators")


A 20-40dB attenuator in-line with the antenna co-ax will reduce the signal enough so that the Arrow Yagi will indicate a bearing at 250M. A simple trick that will also reduce the signal seen by the hand held is to de-tune the hand held away from the nominal frequency by around 10khz.

**Getting a Bearing** **with a directional antenna**

Connect the antenna to your hand held radio and tune to the frequency of your tracker and turn off the squelch. . If the FSK RTTY transmission starts with a series of pips, that are normally used to line up the audio decode in FLDIGI, you will find it easier to pick up a weak signal amongst the noise. Direct the antenna for minimum noise.

If the signal is very strong, you will notice very little difference in noise level, so you need to try the methods described above to reduce the signal getting to the hand held. The Moxon can be rotated thru 180 degrees, it does have noticeably less gain the ‘wrong’ way round.

Once you have a bearing, you should take another for triangulation. The strength of the received signal is no effective indication of distance to the tracker, it could have landed on top of a tree (very strong signals) or dropped into a hole (very weak signals).

Go a 100M or more at right angles to the first bearing and get another. If the second bearing is very different from the first, you are likely very close to the tracker. If the second bearing is roughly the same as the first you are some distance from the tracker and need to get closer.

With the antenna removed a Baofeng behaves as a fairly reliable proximity detector, you wont pick up any signals until you are within 50M or so of the tracker. With antenna removed you should even be able to get a ‘body fade’ (see later) bearing as close as 10M to the tracker.

**What no Directional Antenna ?**

If you don’t have a handy Yagi or Moxon, you can still get a bearing by using a ‘body fade’ technique, although it does need a bit of practice. For this you hold the UHF hand held close to your chest and turn around until you hear the most noise; this is the point where there is maximum attenuation by your body of the received signal. The bearing to the tracker is now directly behind you.

As you get closer to the tracker, the signals will be so strong that there is not much difference in noise level when you turn your body, the hand held radio is being swamped. Desensitise the hand held by removing its antenna and try again to get a bearing.

One combination that I find works well, to cover the difficult last 250M of the search, is to use body fade and the stubby antenna shown for Baofeng, with the possible option of using the attenuator as well. The stubby antenna is in fact a very poor antenna (compared to the Baofeng original) which is exactly what we need for this type of RDF.

**What do I Use ?**

When going out ‘searching’ I would normally take my Arrow yagi and cable, a Baofeng handheld with dodgy stubby antenna and attenuator. I also have my RC model toolbag with me, that has a spare Baofeng in it (they are cheap), the add on pack that takes AA batteries and a Moxon.

**Please Note**

Be warned that **you do need to practice** locating a tracker using these methods **before you have to do it for real.** Its not intuitive exactly what your listening for, especially with the body fade technique, so if you don’t practice in advance, you wont know what to listen for.

Getting good bearings is a lot easier in the open countryside, but getting good bearings in built up areas can be very difficult due to the amount of reflections and reception hot spots there can be. Again practice will help you appreciate the techniques.
 
![map and compass](/images/map-and-compass_thumb.jpg "map and compass")


**And finally ……...**

Having a map of the area you are searching, a compass and knowing how plot bearings on the map is a very good idea.
