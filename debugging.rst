Debugging
=========



Debugging is an important part of programming. 
Due to the lack of interfaces such as screen or sounds, one relies by default on the basic LEDs to investigate program errors. 
But luckily it is also possible, with a bit of extra effort, to establish text communications with the computer through the USB connection.
 
In this section, we will explore different types or errors, and techniques to detect them and try to address them.

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

	**Compile the code below. It should not give you any error. 
	Move it to your controller. **

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
You don't need to look at this now, but you are curious and have a bit of spare time, feel free to read about what `Pulse Width Modulation (PWM) <https://en.wikipedia.org/wiki/Pulse-width_modulation>`_ does.  
This is very handy to control the brightness of LEDs for instance.
 
As it happens, the pin D9 does support PWM, so all works fine. But pin D8 does not.
**Try changing D9 for D8 in the code and observe the result.**

**The code should compile without error. But LED 1 will start flashing with a pattern of 4 long and 4 short blink. 
This is the signal that the controller has experienced a runtime error.**

The compiler does not fully check the suitability of the pins when the code is compiled, causing the microcontroller to crash when it tries to execute the program on inappropriate pins.


Debugging strategies
^^^^^^^^^^^^^^^^^^^^


There is a lot more information online on this topic. You'll find a few ideas there:

https://os.mbed.com/handbook/Debugging




Communications between the computer and the microcontroller
-----------------------------------------------------------

This section is more advanced, but really useful once you get it to work. What is difficult here is that it depends on the computer connected to the board. Different operating systems will use different software (that you may need to  install) in order to talk to the board, and they would behave differently. Give it a try, but don't panic if it does not work for you. You can go through the next activity without reading text from the board.



Read the first half of the mbed doc on `debugging with printf() calls <https://docs.mbed.com/docs/mbed-os-handbook/en/latest/debugging/printf/>`, until the section *Printf() from an interrupt context*.



Example
^^^^^^^


.. admonition:: Exercise

The program below should cycle the three LEDs, but doesn't work quite as expected. You suspect at first that your third LED is faulty.

.. code-block:: c

	#include "mbed.h"
	
	Serial pc(SERIAL_TX, SERIAL_RX);
	
	// Green LED
	DigitalOut led1(LED1);
	// Blue LED
	DigitalOut led2(LED2);
	// Red LED
	DigitalOut led3(LED3);
	
	
	void select_led(int l)
	{
	        if (l==1) {
	                led1 = true;
	                led2 = false;
	                led3 = false;
	        }
	        else if (l==2) {
	                led1 = false;
	                led2 = true;
	                led3 = false;
	        }
	        else if (l==3) {
	                led1 = false;
	                led2 = false;
	                led3 = true;
	        }
	}
	
		
	int main() {
	    pc.baud(9600);
	    int t=1;Compile and run the program below.
	    
	    pc.printf("Start!\r\n", t);
	
	    while(1) {
	          select_led(t);
	          pc.printf("LED %d is ON.\r\n", t);
	          wait(0.5);
	          t=(t+1)%3;
	
	    }
	}


But the output of the printf function looks like this: 


.. code-block:: c

	Start!
	LED 1 is ON.
	LED 2 is ON.
	LED 0 is ON.
	LED 1 is ON.
	LED 2 is ON.
	LED 0 is ON.
	LED 1 is ON.
	LED 2 is ON.
	LED 0 is ON.
	LED 1 is ON.
	...

Use this information to find the problem!


