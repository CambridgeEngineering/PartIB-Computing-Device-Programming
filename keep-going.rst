Keep going
==========

Functions
---------

Let's refresh your mind regarding the declaration and use of functions in C/C++.

- Create a new project on the mbed development site. Select the same template ("Blinky LED test for the ST Nucleo boards"), but give it a new project name. If you were to select a blank template, you would miss the mbed.h header file that contains many important elements for your code.

- Replace the demo code with the code below. What does the **select_led** function do? If you are intrigued by the expression "t%3", look for its definition; it is the remainder after division of t by 3, also called `modulo <https://en.wikipedia.org/wiki/Modulo_operation>`_.


.. code-block:: c

	#include "mbed.h"

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
		 int t=0;
		 while(true) {
			select_led(t);
			wait(0.5);
			t=(t%3)+1;
		}
	}



.. admonition:: Task

	**Modify the program so that select_led(0) turns all the LEDs off, and select_led(-1) turns them all on.**

	**Change the sequence such that the pattern is {all off, led 1, led 2, led 3, all on, all off, etc.}.**


    For a more immersive experience, try your code while visiting
    `this page <https://www.youtube.com/watch?v=q_F9Nrs7ODQ>`_ .


.. admonition:: Task (optional)

   **Program a LED sequence inspired by this** `video clip <https://www.youtube.com/watch?v=oNyXYPhnUIs>`_.

|
|

Physical input with a push button
---------------------------------

The code below exploits a useful inclusion in your development board,
a push button!

.. code-block:: c

    #include "mbed.h"
     
    DigitalIn button(USER_BUTTON);
    DigitalOut led2(LED2);
     
    int main()
    {
      led2=0;
      while(true)
      {
        if (button == 1)
             led2 = true;
        else led2 = false;
        // Fyi, C programmers often like to turn such tests into logical statements:
        //   led2= !(button == 0);
        // The "!" presents the logical negation. 
        wait(0.02); // 20 ms
      }
    }


.. admonition:: Task

   **Create a new project for it, compile it, install it on your
   board, and try it. What happens with you press the button? Is that
   what you expected?**


``USER_BUTTON`` is a constant defined to correspond to the pin number
attached to the blue button.

When pressed button is true (1) and false (0) otherwise. By assigning
its value to the LED, we can control the LED with the button.

The movie clip below (https://www.youtube.com/embed/XmWqP8laxxk) explains some of this using external LED and
switch. Look at it if you would like more information.

.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/XmWqP8laxxk" frameborder="0" allowfullscreen></iframe>

|
|

.. admonition:: Task

	**Edit the code so that the blue LED is on when   
	the button is pressed, but the red LED is on when the button is not  
	pressed (or any other LED combinations you could think about).**                                                             
