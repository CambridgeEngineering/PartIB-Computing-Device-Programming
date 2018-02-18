Activity 2: I\ :sup:`2`\ C bus and sensors
===============================


*Material under development*


Learning objectives
-------------------


The aim of the activity is to illustrate simple ways to monitor, log and transfer sensor data, which is a very common component of most devices.
It builds up on the skills you learned in the first activity - you'll get to use arrays and interrupts again!


This activity will teach you how to interact with a large class of devices communicating with the ubiquitous I\ :sup:`2`\ C bus and protocol.
You will also have to polish your soldering skills, and learn how to navigate a complex data-sheet to find the information you need.



Task to complete
----------------


You have to collect from the Teaching Office a sensor and a strip of 5 jumper wires that you'll need to connect the sensor to your board.
The EIETL has facilities you can use to solder the headers to the sensor breadboard.

The data-sheet of your sensor is available :download:`here <docs/temp_sensor_lm75b.pdf>`.
The sensor is assembled on a breakout board to make it more convenient to use.

Using the sensor and your micro-controller, you will have to:

- record a temperature value every second in a buffer that will always contain the last minute of data.

- if the temperature goes above a threshold value, trigger an interrupt that will get the LEDs to flash an alarm signal (for you to imagine), and dump all the data in the buffer through the USB serial communication so that you computer can analyse it.




What you may need to learn
--------------------------

To complete the task, you may need to learn the following elements

- What are I\ :sup:`2`\ C devices and how to communicate with them?

- Soldering and testing your sensor.



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


Structure and timing of the activity
------------------------------------


**Deadline and recommended approach**

You have until the **end of Easter week 2** to complete the activity.

We recommend that, before the end of Lent term, you have soldered and tested your device, making sure that all is in order!
This should take a couple of hours of your time if all goes well.
As always, please feel free to work as a group and tackle together the parts you may be struggling with on your own.

Completing the task itself can be done during the Easter vacation.
We would expect it to take another 2-3 hours in average, but may vary depending on how much you have to learn.


**Support**

Support sessions in the DPO will be available to you during the first two weeks of Easter.



**Assessment**


You will be asked to answer a quiz on Moodle and submit your code.

We will also ask you to bring your device for a mini demo/viva in the DPO.

