Activity 2: I\ :sup:`2`\ C bus and sensors
===============================




Learning objectives
-------------------


In this second activity, you will learn about a very common aspect of device programming: monitoring, logging and transferring sensor data.
It builds up on the skills you learned in the first activity - you will get to use arrays and interrupts again!


This activity will teach you how to interact with a large class of devices communicating with the ubiquitous I\ :sup:`2`\ C bus and protocol.
You will also have to polish your soldering skills, and learn how to navigate a complex data-sheet to find the information you need.



Task to complete
----------------

The data-sheet of your sensor is available :download:`here <docs/temp_sensor_lm75b.pdf>`.

Using the sensor and your micro-controller, you will have to:

- record a temperature value every second in an array that will contain the last minute of data (older data is replaced by new data once the array is full).

- if the temperature goes above a threshold value of 28 degree Celsius, get the sensor to trigger an interrupt that will get the LEDs on the microcontroller to flash an alarm signal (for you to imagine), and transmit all the data available in the array to your computer by USB serial communication, so that the log may be analysed.

- optional: if you are keen, you may try to capture and plot the temperature data on your computer using a python script.


What you may need to learn
--------------------------

To complete the task, you may need to learn the following elements

- What are I\ :sup:`2`\ C devices and how to communicate with them?

- Soldering and testing your sensor.

- Configuring the sensor using its internal registers

- Responding to hardware interrupts from the sensor



Before getting started with the main task, you are invited to
learn about the prerequisites mentioned above.
Please take them in the right order.
This should give you the background knowledge needed to tackle the
activity.
It may take you 2-3 hours to go through it.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   i2c-intro
   soldering
   i2c-testing
   i2c-com-lm75
   i2c-interrupt-lm75







