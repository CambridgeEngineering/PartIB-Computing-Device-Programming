Keep going
==========








A more complex program
----------------------

The code below exploits a useful inclusion in your development board, a push button!


.. code-block:: c

	#include "mbed.h"
	 
	DigitalIn button(USER_BUTTON);
	DigitalOut led1(LED1);
	 
	int main() {
	  led1=0;
	  while(1) {
	   led1= !(button == 0);
	   wait(0.02); // 20 ms
	  }
	}


.. admonition:: Exercise

	**Create a new project for it, compile it, install it on your board, and try it. What happens with you press the button? Is that what you expected?**


USER_BUTTON is a constant defined to correspond to the pin number attached to the blue button.

When pressed button is true (1) and false (0) otherwise. By assigning its value to the LED, we can control the LED with the button.


The movie clip below explains some of this using external LED and switch. Look at it if you would like more information.

.. raw:: html

	<iframe width="560" height="315" src="https://www.youtube.com/embed/XmWqP8laxxk" frameborder="0" allowfullscreen></iframe>

|
|

.. admonition:: Exercise

	**Would you be able to edit the code so that the blue LED is on when   
	the button is pressed, but the red LED is on when the button is not  
	pressed?**                                                             

