Activity 3: Actuation and feedback
==================================


Learning objectives
-------------------

Microcontroller do logic and mechanics, but you can't really get power out of them. In this activity, you will learn how to deliver power to a device attached to the microcontroller, and how to control this power to achieve a certain goal. This will transform the logical/mathematical capabilities of your micro-controller into actions that have effect on the real world. 
The activity relies on the skills that you have learned in the previous activities - you will use interrupts, read sensors, and maybe even solder again!






Task to complete
----------------

The aim is to implement a simple temperature controller. 

Using a temperature sensor attached against one of the sides of a Peltier cell, your task is to keep constant the temperature it measures by transferring heat using the Peltier cell. The target temperature will be set in the code, and possibly changed by the user using the integrated button. The range of target temperatures should be close to ambient temperature (say within 5 degrees) to keep currents low enough.

If you plan primarily to cool down, it may be next to attach a heat sink on the hot side and the sensor on the other. If you plan to regulate temperatures larger than ambient temperature, do it the other way around. The heat sink helps dissipate the heat transferred on the side we do not wish to regulate, making it easier for the Peltier cell to do its job.

As an extra, you may want to consider ways to set the target temperature through the button. 



What you may need to learn
--------------------------



- Learn about Pulse-Width-Modulation and how to use it to adjust the brightness of a LED.

- Learn what a H-Bridge is and how to use it to adjust the electric power delivered to a device. As an example, you can regulate the speed and direction of a DC motor.

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
   










































