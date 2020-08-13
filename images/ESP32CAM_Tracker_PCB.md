This is a simple PCB that can be used turn an ESP32CAM into a LoRa enabled GPS tracker. With good line of sight between a LoRa receiver, such as ground to a high altitude balloon, you can receive the GPS co-ordinates from hundreds of kilometres away. 

The Arduino software needed to make the ESP32CAM a tracker, reading the GPS, taking a picture, saving it to SD and then sending the GPS coordinates via LoRa has been tested. 

There is a choice of LoRa devices, an RFM98 on the front and SXxxxx on the rear. For the LoRa device antenna there are options to fit a simple wire antenna, an SMA connector antenna or and external antenna va a U.FL connector. 

The ESP32CAM comes with a AMS1117 regulator which needs a input voltage of at least 4.6v which rather limits the battery choices. You can remove the AMS1117 from the ESP32CAM and fit on the ESP32CAM_Tracker_PCB a low dropout regulator such as MCP1700 which would need an input voltage of only 3.5v or so.

On the bottom edge of the PCB there is a 4 pin connector for the GPS and 2 pin connection for a battery, alternatly a JST style silicone battery lead can be secured to the bottom of the PCB. 

The program connector is positioned on the edge of a PCB so that you can easily attach spring loaded test hook, or just fit a 4 pin 0.1" header. You need to disconnect the GPS for programming  or fit a switch which allows you to disconnect the GPS from circuit when programming is required.

There are spaces for additional decoupling capacitors (SMT 1206 size). You can fit two resistors which allow the supply voltage to be read. And additional SMT LED can be fitted if needed. A pad has been provided for a modification; you can attach a wire to pin 33 on the ESP32 and use it as a serial debug output when the GPS is in use on the programming port.

Holes in the ESP32CAM_Tracker_PCB allow you to access the reset switch on the ESP32CAM and see the pin 33 LED. You can fit a boot switch to the PCB for easy programming. 

For a lightweight GPS, you can make up a light weigth  L70/L76 GPS using the breakout **[here](https://www.tindie.com/products/20921/)** or use a Ublox MAX 8Q using the breakout board **here**. 

