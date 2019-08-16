Serial communication
====================



Many protocols exist to allow devices to communicate with each other. In this section, we briefly present the key steps required to exchange information between the microcontroller and a computer connected to it on a USB port. This port was primarilly used to so far to program the device, but as seen in the previous section, it can also be exploited to pass messages as strings of characters. We will first see how to pass strings from the computer to the device, and then develop a simple python interface to send and receive data, and delegate computational tasks.




Receiving strings from the computer
-----------------------------------

The code below shows how to capture information from the user through the Serial (USB) port. The function doing all the work is scanf. It reads from the serial port a string, and uses the format given as first parameter to extract variables in specified format. The syntax is not trivial, and rather typical of C programming, but the function is actually very powerful and flexible as a result. In all cases, a variable needs to be declared to receive the value before the function is called. It is passed to the scanf function as a reference, i.e. an address in memory where the variable has to be written. More on this as part of activity one. 


   .. code-block:: c

	include "mbed.h"

	Serial pc(SERIAL_TX, SERIAL_RX);

	int main() {

		//	Reading a string from the computer
		
		char s[32]; // Array of characters to store the name
		
		pc.printf("\r\nPlease type your name.\r\n");   
		pc.scanf("%s",s);
		pc.printf("Your name is %s.\r\n", s);


		//	Reading integers and flaoting point values

		int n;
		
		pc.printf("\r\nPlease type an integer and I'll tell you what its double is.\r\n"); 
		pc.scanf("%d",&n);
		pc.printf("2 x %d = %d\r\n", n, 2*n);
		
		float f;
		
		pc.printf("\r\nLet's do the same with a floating point number...\r\n");   
		pc.scanf("%f",&f);
		pc.printf("2 x %f = %f\r\n", f, 2*f);

	}





.. admonition:: Exercise

   **Try the code above. Depending on your serial terminal, you may or may not see the characters you type, or may have to enter them in a specific text box. Look for the documentation of your serial terminal if needed. Once you get it to work, modify it to request the name and age of the user, and return a single sentence as a string containing the name and age of the user.**




Creating a simple python interface
----------------------------------


Delegating work to the microcontroller
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The code below uses serial input and output to delegate a very simple task to the microcontroller, adding two numbers together! This is obviouly a very ineffective way to add integers, but serves notheless as a proof of concept.


   .. code-block:: c

	#include "mbed.h"

	Serial pc(SERIAL_TX, SERIAL_RX);

	int main() {

		int n1, n2; 
		
		while(1){     
			pc.scanf("%d",&n1);
			pc.scanf("%d",&n2);
			pc.printf("%d\n", n1+n2);
		}
	}


.. admonition:: Exercise

   **Try the code above and make sure it works fine. What happens if you try to add fractional numbers? Try to make it work for floating point numbers.**








Python serial library
^^^^^^^^^^^^^^^^^^^^^

Here is a piece of python code initiating a serial channel to the microcontroller, sends two strings representing numbers to add, and read the response of the micro-controller, as a string. 

   .. code-block:: python

	import serial
	board = serial.Serial("/dev/ttyACM0", 9600)

	# communicating with strings

	board.write(b'2\n')
	board.write(b'5\n')
	board.readline()

The port name ``"/dev/ttyACM0"`` depends on your operating system and the way it would label USB devices, as discussed in the debugging section. Replace by the port name on your system.

The characters ``"\n"`` are needed as they correspond to carriage return instructions that the microcontroller monitors before scaning the input.

As it is, it is not very elegant, but the coversions back and forth to strings could be packaged into a python function, only exposing to the user a relatively simple interface.

   .. code-block:: python


	def mysum(n1, n2):
		board.write(str(n1) + b'\n')
		board.write(str(n2) + b'\n')
		return( int(board.readline()) )







.. admonition:: Exercise

   **Use this python interface to communicate with your microcontroller. Add some code to the microcontroller program to toggle the state a LED each time a sum is generated, as a visual confirmation that it did the work.**




A more complex example
----------------------


Why not try a more challenging task... 


.. admonition:: Exercise - part a

   **Create code on the microcontroller to calculate the n first elements of a mathematical series, for instance the Fibonacci series. Read n from the serial port, and output the resulting elements on the serial port too.**


.. admonition:: Exercise - part b

   **Write a python function taking n as a parameter, and returning the n first element of the series as an array or list. The function must delegate the calculating to the microcontroller.**




