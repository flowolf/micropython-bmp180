micropython-bmp180 (ESP8266)
==================

Module bmp180
-----------------
bmp180 is a micropython module for the Bosch BMP180 sensor. It measures
temperature as well as pressure, with a high enough resolution to calculate
altitude.

This code is based on [pyboard code](https://github.com/micropython-IMU/micropython-bmp180) by Sebastian Plamauer, [@turbinenreiter](https://github.com/turbinenreiter)

Breakoutboard: http://www.ebay.at/itm/322193678288?_trksid=p2057872.m2749.l2649&ssPageName=STRK%3AMEBIDX%3AIT

data-sheet: http://ae-bst.resource.bosch.com/media/products/dokumente/bmp180/BST-BMP180-DS000-09.pdf

If you have any questions, open an issue.

### Wiring the sensor to the ESP8266
(just an example, I2C is implemented in software an can be run on [any GPIO](http://docs.micropython.org/en/v1.8/esp8266/esp8266/quickref.html?highlight=i2c#i2c-bus) pin)

| esp8266| bmp180 |
|:------:|:------:|
| 3V3    | VIN    |
| GND    | GND    |
| GPIO14 | SCL    |
| GPIO12 | SDA    |

### Working Setup

![wiring](https://raw.githubusercontent.com/flowolf/micropython-bmp180/master/wiring_to_esp8266_nodemcu.png "Wiring")

### Quickstart

Example:
```python
from bmp180 import BMP180
from machine import Pin
bmp180 = BMP180(scl=Pin(14),sda=Pin(12),freq=100000)
bmp180.oversample_sett = 2
bmp180.baseline = 101325
temp = bmp180.temperature
p = bmp180.pressure
altitude = bmp180.altitude
print(temp, p, altitude)
```

Important Notice:  
Make sure to either use this in fast loops or call gauge() in fast loops to make sure to always get current values. If you call pressure() once and then again 10 seconds later, it will report a 10 seconds old value. Look at the gauge() function in the source to understand this. Alternatively use the blocking_read() method.

Classes
-------
``BMP180``  
Module for the BMP180 pressure sensor.  
![UML diagram](https://raw.githubusercontent.com/flowolf/micropython-bmp180/master/classes_BMP180.png "UML diagram")


Methods
--------------

``compvaldump()``
Returns a list of all compensation values.  

``gauge()``  
Generator refreshing the measurements. Does not need to be called manually.

``temperature``  
Nonblocking read of temperature in degree C.  

``pressure``  
Nonblocking read of pressure in Pascal (divide result by 100 for millibar/hP).  

``blocking_read()``  
Trigger a read and wait until data is available.  

``altitude``  
Altitude in m.  


Instance variables
------------------
``chip_id``  
ID of chip is hardcoded on the sensor.

``oversample_sett``  
Sets the accuracy. Default: 0  
* 0 lowest accuracy, fastest
* 1
* 2
* 3 highest accuracy, slowest

``baseline``  
Pressure at Main Sea Level. The default is 101325 Pa, but you can use your local QNH in Pa.  
To get different altitudes, use this as baselines:

| altitude |       baseline |  
|:--------:|:--------------:|  
| absolute | local pressure |  
| true     |        QNH*100 |  
| pressure | 101325 or None |  
