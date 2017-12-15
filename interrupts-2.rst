Interrupts (continued)
======================


LED toggle 2.0
--------------

Let's assume that we want to toggle the state ``LED1`` with the push button,
while the microcontroller is busy doing something very important,
such as monitoring the temperature of a nuclear plant.
To illustrate the progress of this other important task with the hardware present on the board,
we will simply get the micro-controller to flash another LED,
and assume that it is important to do it in a very regular manner.

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


Try the code. ``LED3`` should blink, and the button toggle the state of ``LED1``.
So all seems to work. But...




Focus now on what happens to the very important task, flashing the LED.
It stops for a little while when the button is pressed,
when the micro-controller is running the wait statement in the callback function.
We need this time delay to prevent bouncing.
But this prevent the important tasks to be performed properly.
It woud also block other interrupts that may be required to handle additional events in a more
complex application.
**It is a general rule to spend as little time as possible in interrupts.**

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

But how to reattach the interrupt to the callback function after a while,
without using a wait statement?
Time to talk about timers and time interrupts!

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

Is the problem fixed?

**Comment about function declarations**

Note the line:

.. code-block:: c

	void onButtonStopDebouncing(void);

It seems that we declare the function twice. Why?

This is because the functions onButtonStopDebouncing and onButtonPress
call each other.

If you remove the first declaration of onButtonStopDebouncing, the compiler will
tell you that onButtonStopDebouncing is not defined in function onButtonPress,
which is true, because it is define further down in the code. 
But if you swap the order of the function, then the compiler will complain that
onButtonPress is not declared in onButtonStopDebouncing.

This is why we have to introduce an early declaration of
onButtonStopDebouncing
before we write the code of the function onButtonPress.
It tells the compiler what the function onButtonPress will be (types of parameters and output)
which is essentially the information needed to compile onButtonPress properly.






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

