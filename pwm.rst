Pulse-Width-Modulation and LED brightness
=========================================


What is Pulse-Width-Modulation ?
--------------------------------

Pulse-width-modulation (PWM) is probably the most common way to produce an average analog voltage in electrical circuits via fast switching. 
The idea is simple: if you switch the voltage of a circuit between ON (V\ :sub:`ref`\ volts) and OFF (0 volts) sufficiently fast, you will generate an average voltage in the circuit proportional to the ratio between the length of time the circuit was ON and the length of time the circuit was OFF. 

Let's consider a few examples. Take a PWM frequency of 10kHz; this means that the ON/OFF cycle repeats 10000 times a second).

- A 50% duty cycle means that the circuit is ON for half of the cycle and OFF otherwise (first row in the image below). Thus, the generated averaged voltage is about 0.5V\ :sub:`ref`\.

- A 25% duty cycle means that the circuit is ON for a fourth of each cycle and and OFF otherwise (last row in the image below). The generated voltage is about 0.25 V\ :sub:`ref`\.



.. figure:: https://upload.wikimedia.org/wikipedia/commons/b/b8/Duty_Cycle_Examples.png
   :scale: 50 %
   :alt: Duty_cycle_PWM 

   PWM. Source: Wikipedia


This technique is so popular that you can find a full page on Wikipedia (https://en.wikipedia.org/wiki/Pulse-width_modulation) dedicated to it and several tutorials available on internet like `Arduino <https://www.arduino.cc/en/tutorial/PWM>`_ and `Sparkfun <https://learn.sparkfun.com/tutorials/pulse-width-modulation/all>`_. You may find this video useful:

.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/GQLED3gmONg" frameborder="0" allowfullscreen></iframe>

..

..


|
|



The PWM library
---------------

The `MbedOS PWM library <https://os.mbed.com/docs/mbed-os/v6.15/apis/pwmout.html>`_ simplifies the use PWM on the microcontroller. 

First, you need to declare your PWM output


   .. code-block:: c
   
	PwmOut pwmled(LED2);

The instruction above sets LED2 as pwm output. Any other digital output compatible with PWM would work. Check your microcontroller manual to know which pins are compatible with PWM.

Then, you need to set the PWM period


   .. code-block:: c
   
	pwmled.period_us(1000);

The instruction above sets a ON/OFF cycle every 1000 microseconds, that is, 1000 cycles each second.

Finally, the duty cycle is defined by


   .. code-block:: c
   
	pwmled.write(0.1f); 

The argument of the write function must be a float between 0 and 1. The instruction above sets the duty cycle at 10%. 

You can also read the current PWM duty cycle through the instruction


   .. code-block:: c
   
	pwmled.read(); 

which returns a floating-point value. 
For more details, please refer to the `PwmOut API <https://os.mbed.com/docs/mbed-os/v6.15/apis/pwmout.html>`_.


LED brightness through PWM
--------------------------

By now the following code should be quite readable.


   .. code-block:: c

	#include "mbed.h"

	PwmOut pwmled(LED2);

	int main() {
		
		pwmled.period_us(1000);
		pwmled.write(0.1f); 
		printf("pwm set to %.2f %%\n", pwmled.read());    
	}

The code switches ON and OFF the LED 1000 times a second. Within each cycle the LED is ON only for 10% of the time. Your eyes cannot see such fast frequencies and you will perceive an average brightness.


.. admonition:: Task

   **Modify the code to make brightness slowly pulsating from low brightness to high brightness and back.**



