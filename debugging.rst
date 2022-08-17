Debugging
=========



Debugging is an important part of programming.  Due to the lack of
interfaces such as screen or sounds, one relies by default on the
basic LEDs to investigate program errors.  But luckily it is also
possible, with a bit of extra effort, to establish text communications
with the computer through the USB connection.

In this section, we will explore different types or errors, and
techniques to detect them and try to address them.

Errors
------

Compile time errors
^^^^^^^^^^^^^^^^^^^

.. admonition:: Exercise

   **Try to compile the code below. Read the three errors; they can be
   seen in the compile output section at the bottom of the window, or
   by hovering your mouse over relevant red line in the scroll
   bar. Fix the errors.**

   .. code-block:: c

	#include "mbed.h"

	DigitalOut myled(LED1);

	int main() {
		while(true) {
			led1 = 1; // LED is ON
			wait(0.2); // 200 ms
			led1 = OFF; // LED is OFF
			wait(1.0) // 1 sec
		}
	}


Execution time errors
^^^^^^^^^^^^^^^^^^^^^

.. admonition:: Exercise

   **Compile the code below. It should not give you any error.  Move
   it to your controller.**

   .. code-block:: c

	#include "mbed.h"

	// Pin D9 supports Pulse Width Modulation (PWM)
	// Pin D8 does not support Pulse Width Modulation (PWM) --> run time error expected.

	PwmOut led(D9);

	int main() {
		led = (float)0.5;
		while(true) {    }
	}

You would not see much, but it sends on pin D9 a square signal that
you could detect on an oscilloscope.  If you are curious and have a
bit of spare time, feel free to read about what `Pulse Width
Modulation (PWM)
<https://en.wikipedia.org/wiki/Pulse-width_modulation>`_ does; you
don't need to look at this now though.  This is very handy to control
the brightness of LEDs for instance.

As it happens, the pin D9 does support PWM, so all works fine. But pin
D8 does not.  **Try changing the pin D9 to D8 in the code and observe the
result.**

**The code should compile without error. But the board will then display its default runtime-error behaviour.
This behaviour may depend on the version of the hardware and libraries you are using. 
You may notice the Ethernet port LED flashing irregularly, or LED 1 flashing
with a pattern of 4 long and 4 short blinks. This is the signal that
the controller has experienced a runtime error.**

The compiler does not fully check the suitability of the pins when the
code is compiled, causing the microcontroller to crash when it tries
to execute the program on inappropriate pins.

These errors are more subtle to detect as the signals from the board are not clearly documented and not always consistent.
If you struggle to identify the runtime error, don't get stuck and progress to the next section.
Just remember that such errors exist, and note the importance of testing as you go along, for instance using LED blink patterns to monitor your progress along the executiong of the code, or using the technique introduced below.


Debugging strategies
^^^^^^^^^^^^^^^^^^^^

There is a lot more information online on this topic. You will find a
few more ideas there:

https://os.mbed.com/handbook/Debugging






.. _serial-tuto-label:


Using the serial port to monitor and debug programs 
---------------------------------------------------


This section is more advanced, but really useful once you get it to
work. Communications between the microcontroller and computer will be developed further in the following tutorial.

Different operating systems will use different
software (that you may need to install) in order to talk to the board.
Each operating system will also have different naming conventions to identify the port used to connect to the board.
Therefore, it is difficult to provide here generic instructions, and you will have to find your way through other docs and tutorials.

You can get your board to send text messages to your computer using Serial communications.
What is difficult here is that it depends on the computer
connected to the board. Different operating systems will use different
software (that you may need to install) to communicate with the board,
different names for the port used to connect the board, and they would
behave slightly differently. Give it a try, but don't panic if it does not work
for you straight away. You can go through the next activity without
reading text from the board.
>>>>>>> master



Read the first half of the mbed doc on `debugging with printf() calls
<https://docs.mbed.com/docs/mbed-os-handbook/en/latest/debugging/printf/>`_,
until the section *Printf() from an interrupt context*.

You will need to use a Terminal to handle the communication with the board
and display text. This page may be useful to install one:

https://os.mbed.com/handbook/Terminals


Give it a try, but don't panic if it does not work
for you straight away. You can go through the activity 1 without
reading text from the board, but serial communication is required for activities 2 and 3.



Example
^^^^^^^

.. admonition:: Exercise

   The program below should cycle the three LEDs, but doesn't work
   quite as expected. You can try it on your device. 
   The third LED is not blinking, and you may assume at first that
   it is faulty.

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
	    int t=1;

	    pc.printf("Start!\r\n", t);

	    while(true) {
	          select_led(t);
	          pc.printf("LED %d is ON.\r\n", t);
	          wait(0.5);
              // cycles the values of t
              // check how the modulo operation (%) works if unsure
	          t=(t+1)%3;

	    }
	}


   But the output of the program looks like this:


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




Catching the output from Python
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Serial communications can be used for much more than debugging. 
The example below shows how to catch the text in python (running on your computer) 
using the `pySerial library <https://pythonhosted.org/pyserial/>`_. 
You could process it further if needed. 


.. code-block:: python

   import serial
   board = serial.Serial("/dev/ttyACM0", 9600)
   # This creates an object able to establish a serial communication channel
   # with the board. The first parameter depends on your operating system
   # and probably needs to be updated.
   # The second is the baud rate. It needs to match the board's settings.

   while True:
       line = board.readline()
       print(line)

Feel free to test this script. If you are using Linux, you may need to
run it as a super-user to gain access to the port, i.e. launch it from
a terminal using `sudo python script_name.py''.

Of course you can also communicate the other way around. Serial
communication is very handy to get devices to interact with computers,
or with each other. More information is available on the arm/mbed
website:

https://os.mbed.com/handbook/SerialPC#serial-communication-with-a-pc
