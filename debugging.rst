Debugging
=========



Debugging is an important part of programming. 
Due to the lack of interfaces such as screen or sounds, one relies by default on the basic LEDs to investigate program errors. 
But luckily it is also possible, with a bit of extra effort, to establish text communications with the computer through the USB connection.
 
In this section, we will explore different types or errors, and techniques to detect them and try to address them.

There is a lot of information online on this topic!

https://os.mbed.com/handbook/Debugging

Errors
------

Compile time errors
^^^^^^^^^^^^^^^^^^^

.. admonition:: Exercise

	Try to compile the code below. Read the three errors, and fix them!

.. code-block:: c

	#include "mbed.h"

	DigitalOut myled(LED1);

	int main() {
		while(1) {
			led1 = 1; // LED is ON
			wait(0.2); // 200 ms
			led1 = OFF; // LED is OFF
			wait(1.0) // 1 sec
		}
	}


Execution time errors
^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Exercise

	Compile the code below. It should not give you any error. 
	Move it to your controller. 

.. code-block:: c

	#include "mbed.h"

	// Pin D9 supports Pulse Width Modulation (PWM)
	// Pin D8 does not support Pulse Width Modulation (PWM) --> run time error expected.

	PwmOut led(D9);

	int main() {
		led = (float)0.5;
		while(1) {    }
	}

You would not see much, but it sends on pin D8 a square signal that you could detect on an oscilloscope. 
This is what Pulse Width Modulation (PWM) does. 
This is very handy to control the brightness of LEDs for instance.
Look for it on the internet if you wnat to know more about it, but this is not important for us right now. 
As it happens, the pin D9 does support PWM, so all works fine. But pin D8 does not.
Try changing D9 for D8 in the code.

**LED 1 will start flashing with a pattern of 4 long and 4 short blink. 
This is the signal that the controller has experienced a runtime error.**

The compiler does not check for the suitability of the pins when the code is compiled, causing the microcontroller to crash when it tried to execute the program.

Communications between the computer and the microcontroller
-----------------------------------------------------------

This is more advanced, but really useful once you get it to work.

Printing text on a terminal
^^^^^^^^^^^^^^^^^^^^^^^^^^^

For a beginner, a useful way to debug a program is to print statements using the printf function:

.. code-block:: c
	printf("test\r\n");

The above statement prints out the string "test" followed by a carriage return and a line feed to standard out. 
It is included in the code you copied over to the Mbed compiler above. 
To find out how to view the output of printf statements, see the following support page (precise instructions depend on your operating system):

https://docs.mbed.com/docs/mbed-os-handbook/en/latest/debugging/printf/

Click "Compile" in the Mbed Compiler enironment. 
Save the executable to the Mbed drive and you should see all three leds (green, blue, red) switch on and off in tandem at a regular interval. By following the instructions in the above link you should also see the text "test" appear on a separate line at the same regular interval. Ensure you can see the text sent to standard out via the printf function, as this will greatly simplify debugging later.


Serial communication
^^^^^^^^^^^^^^^^^^^^

Serial is a common protocal to communicate with microcontrollers and exchange data in a bidirectional manner. 
It is easy to implement on the microcontroller, but the most challenging part is to get your computer to talk to it!

There are loads of tutorials online, for each platform. Try to find a way that works for you!

[add links and comments]

