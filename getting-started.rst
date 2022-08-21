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


Unboxing and powering the microcontroller
^^^^^^^^^^^^^^^^^^^^

- Remove the micro-controller from its packaging, and connect the
  micro-USB cable to USB PWR slot. Connect the other end to your
  computer. The microcontroller is powered on.

- If your micro-controller is brand new, it should be loaded already with a default
  program that blinks one of the light emitting diodes (LED). The device has 3 LEDs accessible
  to the user. Press the blue button at the bottom left corner to
  select another the LED. It should also blink at a different
  frequency. This is your first interaction with your new
  microcontroller! 
  If your device is not new, the behaviour may be different. 
  We'll now see how to set the program that is executed when you start the system.



.. tip::

   Keep the plastic packaging to store and transport the
   microcontroller.



Working with ARM's online mbed development environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


There are many environments you can use to program the microcontroller. This activity assumes that you are using the online environment provided by ARM to program their chips.


- Open an account on the latest online platform, Keil Cloud Studio. Visit https://studio.keil.arm.com

- Click login/sign-up. Create a new account if you don't have one
  already.

- If this is the first time using this environment, there won't be much to do yet. Let's create a new project.

You are ready to start coding!



A first project
---------------

Creation of a new project
^^^^^^^^^^^^^^^^^^^^^^^^^

- Once you are logged in, select from the menu click on `File`, `New...`, `Mbed Project`.

- In the dialog window, select the example in the dropdown menu called mbed-os-example-blinky, in the mbed 5 section. 
  (Do not select Mbed OS 6 or Mbed 2 versions for this tutorial.)

- The default project name should be mbed-os-example-blinky-5. You can change it if you want, but keeping it as it is is fine too. If you would like to use version control, feel free to initialize a Git repository alongside the project.

- Once the project is created, select is as your active project using the drop down list on the top of the left side-bar.

- Select the relevant target hardware, which is the specific device for which the executable has to be created. You can do this using the drop down list below the active project selector on the left side-bar. Type or look for your microcontroller, here the NUCLEO-F746ZG.

- In the middle section of the left side-bar, you will find a list of all the files in your new project (and more generally a list of all your projects too). Open the "main.cpp" file by clicking on its name. The code should look like this:


.. code-block:: c

	/* mbed Microcontroller Library
	 * Copyright (c) 2019 ARM Limited
	 * SPDX-License-Identifier: Apache-2.0
	 */

	#include "mbed.h"
	#include "platform/mbed_thread.h"


	// Blinking rate in milliseconds
	#define BLINKING_RATE_MS   	  500


	int main()
	{
	    // Initialise the digital pin LED1 as an output
	    DigitalOut led(LED1);

	    while (true) {
		led = !led;
		thread_sleep_for(BLINKING_RATE_MS);
	    }
	}

This code simply gets one of the LEDs to blink, as suggested by the project's name.
However, the style of the code and the use of threads to control the timing goes beyond the scope of this section.

To get us started, we will use the code below as a first example. Please delete the sample code above from the editor window, and replace it with the code below.


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


- The next step is to compile to program, i.e. create the set of machine instructions specific to the hardware you selected. To do this, you will find a blue button with a little hammer symbol on it just below the target selection drop-down menu on the left side-bar. Click it. There is a hammer symbol because we are "building" the compiled program from the course. Every good builder should have a hammer... 
  
- Whilst the code if compiling, lots of messages will be printed in the output panel under the editing window. If there is no error in your code, it will eventually tell you that the build was a success!
 
- A file is then downloaded by the web browser on your computer, ready to be installed on your microcontroller. The file name would be something like mbed-os-example-blinky-5.NUCLEO_F746ZG.bin

If you pay attention, you will notice a number of warning messages related to the use of the function "wait". These are listed in the Problems panel, next to the output panel. As you will see in this lab, this function is ineffective as it keeps the processor busy doing nothing. The original template code did a better job. You can read more about this `in the documentation <https://os.mbed.com/docs/mbed-os/v6.5/feature-i2c-doxy/group__platform__wait__api.html>`_ if you want, but for now we will continue to use the wait function.



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
  <https://os.mbed.com/docs/mbed-os/latest/apis/digitalout.html>`_. The pin number is
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
