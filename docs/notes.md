# designing the current sense path
the INA180A4 has 200:1 gain. this puts a 300mA current across the 0.05Ohm shunt at 3.3V, close to STM32 max logic level. this is good enough, we can divide it down later if we want to push more current i.e. with pwm.
the al8860Q is a feedback controller with an inductor and sensing resistors. we need to set 333mA with a 0.3ohm resistor btwn VIN and SET pins as described in datasheet:
![datasheet example](al8860q_example.png)
we will also need an inductor to support this driver and a 4.7-10uF cap as close as possible to the input. also needs a "fast low-capacitance shottky diode with low reverse leakage current."
inductor selection: 33uH to 100uH, proportional to supply voltage. "as close to IC as possible with low resistance interconnect"
i think there should be short circuit current monitoring for charge and discharge, but it should only need to be pack-level.
then we monitor cell voltages and do some active balancing... or passive for ease of execution
