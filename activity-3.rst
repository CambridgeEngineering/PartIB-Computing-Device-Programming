Activity 3: Actuation and feedback
==================================


Learning objectives
-------------------



In this third activity you will learn about actuation, which transforms the logical/mathematical capabilities of your micro-controller into actions that have effect on the real world. The activity relies on the skills that you have learned in the first and second activities - you will have to use interrupts, read sensors, and solder again!

You will implement a temperature controller. The controller will acquire data from the temperature sensor and will drive a Peltier cell to achieve the desired temperature.  





Task to complete
----------------




- Adjust the brightness of a LED and of a halogen bulb.

- Use a Peltier cell to transfer a constant amount of heat.
 
- Temperature regulation: use temperature sensor and Peltier cell to regulate the temperature of a given test-surface to a desired temperature.




What you may need to learn
--------------------------



- Learn about Pulse-Width-Modulation and how to use it to adjust the brightness of a LED.

- Learn what a H-Bridge is and how to use it to adjust the brightness of a halogen bulb.

- Learn what a Peltier cell is and how to drive it to transfer heat.

- Learn how to implement a feedback control loop: read sensor, compute the right control action, drive the actuator, and back.

These activities are progressive, and each relies on the former one. Please take them in the right order. 






.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   pwm
   h-bridge
   peltier
   temp-control
   










































