# designing the current sense path
the INA180A4 has 200:1 gain. this puts a 300mA current across the 0.05Ohm shunt at 3.3V, close to STM32 max logic level. this is good enough, we can divide it down later if we want to push more current i.e. with pwm.  
the al8860Q is a feedback controller with an inductor and sensing resistors. we need to set 333mA with a 0.3ohm resistor btwn VIN and SET pins as described in datasheet:  
![datasheet example](al8860q_example.png)
we will also need an inductor to support this driver and a 4.7-10uF cap as close as possible to the input. also needs a "fast low-capacitance shottky diode with low reverse leakage current."  
inductor selection: 33uH to 100uH, proportional to supply voltage. "as close to IC as possible with low resistance interconnect"  
i think there should be short circuit current monitoring for charge and discharge, but it should only need to be pack-level.  
then we monitor cell voltages and do some active balancing... or passive for ease of execution  

we use the MC34063AD to take usb in 5v to 12v for the series battery stack - this is a boost conv control IC so we need to make the boost circuit  
[digikey page](https://www.digikey.com/en/products/detail/onsemi/MC34063ADR2G/919066)

okay so the above idea may not be great. our input current has to be 1.8A, since we have Vin = 5, Vout = 12, ballpark efficiency of 0.85, and Iout = 0.65A, and Iin = (Vout x Iout)/(Vin x efficiency)  

this means we have an Isw of (Iin + Il/2), estimating Il to be 0.3 x 1.8A, Isw = (1.8+0.27) = 2.07A  

this is much higher than the Isw of the MC34063  

this might be expensive. one alternative is to put transistors that switch the battery cells from series to parallel when charging. another is to instead run the cells in parallel and have a boost converter take the battery output up to 12V-ish to power series LEDs.  

if we switch the cell configuration, we can't use the batteries at their series voltage while charging them. well we probably can't use them at all while charging; the current path will probably only go to the output in series config.  

if we do 3s like we said, we need a 5v in to 12v out regulator that can do about 2A Isw  

one regulator that goes 5v to 12v is the MT3608, which can do 650mA Iout at 89% efficiency. not sure how hot it would get though
[mt3608 datasheet](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/8907/MT3608.pdf)
however it's sufficiently cheap at around 40 cents per  
it requires external passives like the others  
we could also use two of them in parallel!  
