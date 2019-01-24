Getting started
===============



What is a microcontroller?
--------------------------

Watch this short video (https://www.youtube.com/embed/jKT4H0bstH8) if you are not sure what a micro-controller is.

.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/jKT4H0bstH8" frameborder="0" allowfullscreen></iframe>



|
|


ARM & mbed microcontrollers
---------------------------


Unboxing and testing
^^^^^^^^^^^^^^^^^^^^

- Remove the micro-controller from its packaging, and connect the
  micro-USB cable to USB PWR slot. Connect the other end to your
  computer. The microcontroller is powered on.

- The micro-controller is loaded at the first start with a default
  program that blinks on of the LEDs. The device has 3 LEDs accessible
  to the user. Press the blue button at the bottom left corner to
  select another the LED. It should also blink at a different
  frequency. This is your first interaction with your new
  microcontroller!



.. tip::

   Keep the plastic packaging to store and transport the
   microcontroller.



Working with ARM's mbed development environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This video (https://www.youtube.com/embed/BAzKg3vcB88) will explain to you the gist of how to program ARM based
microcontrollers using their mbed development environment.

.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/BAzKg3vcB88" frameborder="0" allowfullscreen></iframe>



Register an account on the mbed development platform
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Open an account. Visit http://os.mbed.com/

- Click login/sign-up. Create a new account if you don't have one
  already.

- Once you are logged in, click on ``Compiler'' (top horizontal menu)
  to access the development environment.

- If this is the first time you use the board with the online mbed
  environment: at the top right of the screen, click 'no device
  selected', then 'add platform' and find device 'NUCLEO-F746ZG'.

You are ready to start coding!



A first project
---------------

Creation of a new project
^^^^^^^^^^^^^^^^^^^^^^^^^

- Create new project by clicking on "New",

- Make sure that the platform is correct, and select the template "Blinky LED test for the ST Nucleo boards",

- Open the "main.cpp" file. The code should look like this:

.. code-block:: c

	#include "mbed.h"

	DigitalOut myled(LED1);

	int main() {
		while(true) {
			myled = 1; // LED is ON
			wait(0.2); // 200 ms
			myled = 0; // LED is OFF
			wait(1.0); // 1 sec
		}
	}


- Press the compile button. If there is no error in your code, a file
  is then downloaded on your computer, ready to be installed on your
  microcontroller.



Dissecting the sample code
^^^^^^^^^^^^^^^^^^^^^^^^^^


This code is fairly self-explanatory, if you remember some of what you
learned with the `Mars Lander exercise <https://www.vle.cam.ac.uk/pluginfile.php/1510531/mod_resource/content/6/handout.pdf>`_.
Do not hesitate to consult online documentation about C/C++
when appropriate. There are so many good sources available to you!
See for instance:

http://www.tutorialspoint.com/cprogramming/

Here are a few comments that may be helpful at this point:

- "main()" is the function that is
  executed when the microcontroller starts.

- In C/C++, a block is delimited by curly brackets, {}, and not
  indentation as in python.
  Python style indentation is however good practice for the readability of your code.

- The main program contains a single `"while" loop <https://www.tutorialspoint.com/cprogramming/c_while_loop.htm>`_.
  The term between parentheses after while should be 0 or false for the
  loop to end, so this loops never ends.

- The variable "myled" controls the state of LED1.
  Although it is manipulated as an integer, it is an
  instance of the class `DigitalOut
  <https://os.mbed.com/handbook/DigitalOut>`_. The pin number is
  specified when the object is declared, and remains attached to
  it. LED1 is a shortcut for the pin number associated with the user
  LED1. These associations are board specific, and defined in the
  "mbed.h" header file - so we don't need to worry about them.



Installing the code on your micro-controller
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Connect the micro-controller to your computer using a micro-USB
  cable. The board should be visible as a USB drive on the
  computer. If it isn't, you may need to install specific drivers;
  consult `this page
  <https://os.mbed.com/docs/latest/tutorials/windows-serial-driver.html>`_
  to get support. If you are using Windows on versions older than Win
  10, try ignoring warnings such as "*Driver not installed
  correctly*"; it may work well enough already.

- Drag and drop the .bin file obtained at the previous step on the
  board

- LED at top right corner should be temporarily flashing to indicate
  that the transfer is happening. The program starts automatically
  after that.

- You should see a LED1 blinking!


.. admonition:: Task

   **Explore different blinking frequencies and try the other LEDs, LED2 and LED3.**

.. To develop your understanding of this code and its execution,
   please look at the following movie. They used different pins on a
   different board, as well as an external LED on a breadboard, but
   that is exactly the same problem otherwise.

.. .. raw:: html

.. 	<iframe width="560" height="315" src="https://www.youtube.com/embed/kP_zHbC_5eM" frameborder="0" allowfullscreen></iframe>
