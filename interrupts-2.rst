Interrupts (continued)
======================


LED toggle
----------

Let's assume that we want to toggle the LED while the microcontroller is
busy doing something very important, such as flashing another LED!
(there is not much we can do with 3 LEDs, but you get the idea...)

We could start with the following code:


.. code-block:: c

	#include "mbed.h"
	 
	InterruptIn button(USER_BUTTON);

	DigitalOut led1(LED1);
	DigitalOut led3(LED3);
	 
	void onButtonPress() 
	{
		led1 = !led1;
		wait(0.3);
	}
	 
	int main() 
	{
		button.rise(onButtonPress);  
		while(true) 
		{   // This is where important calculations are made...
			led3 = !led3;
			wait(0.1);
		}
	}



Here, there is no debouncing code by default.
Try the code, and observe that the toggle fails as in the earlier example.
Uncomment the wait statement in the callback function.
Fixed!
Or is it?

Focus now on what happens to the very important task, flashing the LED.
It stops while running the wait statement in the callback function.
This is time wasted, and time that may cause problems down the line.
What if other interrupts are required to handle other events in a more
complex application?
You could miss them if you stay too long in the interrupt.
It is a general rule to spend as little time as possible in interrupts.

The wait statement is only here to prevent the button to trigger multiple
interrupts when pressed.
We could do this differently: get the callback function to deactivate the
button interrupt for a short time, and then turn it back on again.
During this time interval, we want of course the main function to continue its
important job of flashing the LED.

We could deactivate an interrupt with the following statement:

.. code-block:: c

	button.rise(NULL);

which essentially replaces the pointer to callback function with a pointer to nothing.

But how to reattach the interrupt to the callback function without using
the wait statement?
Time to talk about timers and time interrupts.

Time interrupts
---------------

A time interrupt allows you to trigger a callback function after a given
amount of time.

Look at the following code, and try it on your board.


.. code-block:: c

	#include "mbed.h"
	 
	DigitalOut led1(LED1);
	DigitalOut led3(LED3);
	 
	InterruptIn button(USER_BUTTON);

	Timeout button_debounce_timeout;
	float debounce_time_interval = 0.3;


	void onButtonStopDebouncing(void);

	void onButtonPress(void)
	{
		led1 = !led1;
		button.rise(NULL);
		button_debounce_timeout.attach(onButtonStopDebouncing, debounce_time_interval);
		
	}

	void onButtonStopDebouncing(void)
	{
		button.rise(onButtonPress);
	}
	 
	int main() 
	{
		button.rise(onButtonPress);  
		while(true) 
		{   // This is where important calculations are made...
			led3 = !led3;
			wait(0.1);
		}
	}






No time to waste!
-----------------

The solution above is very satisfactory. We are not wasting time
any more in the interrupt.
But we are still spending time waiting for no reason in the main function.
It is not as bad as in the interrupt, but could we make sure that the
main function is fully available for more interesting tasks to be carried?

Try the code below. Search the mbed documentation to know more about the
``Ticker`` class. Essentially the whole program is now managed by
interrupts. Note that the main function could still access the state of
the button or LEDs at any time. 


.. code-block:: c


	#include "mbed.h"
	 
	DigitalOut led1(LED1);
	DigitalOut led3(LED3);
	 
	Timeout button_debounce_timeout;
	float debounce_time_interval = 0.3;

	InterruptIn button(USER_BUTTON);

	Ticker cycle_ticker;
	float cycle_time_interval = 0.1;



	void onButtonStopDebouncing(void);

	void onButtonPress(void)
	{
		led1 = !led1;
		button.rise(NULL);
		button_debounce_timeout.attach(onButtonStopDebouncing, debounce_time_interval);
		
	}

	void onButtonStopDebouncing(void)
	{
		button.rise(onButtonPress);
	}


	void onCycleTicker(void)
	{
		led3 = !led3;
	}

	 
	int main() 
	{   
		button.rise(&onButtonPress);  
		cycle_ticker.attach(onCycleTicker, cycle_time_interval);

		while(true) 
		{   // Even more important code could be placed here
		}
	}

