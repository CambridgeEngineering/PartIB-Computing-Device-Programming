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
It would also block other interrupts that may be required to handle additional events in a more
complex application.
**It is a general rule to spend as little time as possible in interrupts. Comments such as wait, or even printf, may cause your micro-controller to not behave properly.**

The wait statement is only here to prevent the button to trigger multiple
interrupts when pressed.
We could do this differently: get the callback function to deactivate the
button interrupt for a short time, and then turn it back on again.
During this time interval, we want of course the main function to continue its
important job of flashing the LED.

We could deactivate an interrupt with the following statement:

.. code-block:: c

	button.rise(NULL);

NULL is a generic C/C++ constant.
When a pointer value is NULL, it indicates that the pointer points to nothing.
The code line above therefore replaces the address of the callback function with a value
that unambiguously indicates that there is no call-back function to call.


But how to reattach the interrupt to the callback function after a while,
without using a wait statement?
Time to talk about timers and time interrupts!

Time interrupts
---------------

Time interrupts allows us to trigger a callback function after a given
amount of time.
Two main methods are available with mbed:

- **Timeout** if a callback function needs to be called only once after a time delay,

- **Ticker** if the callback function needs to be called at regular time intervals.


To use a time interrupt, we need to declare a variable of type Timeout.

.. code-block:: c

	Timeout event_timeout;

We can now attach to event_timeout a callback function and indicate the time
interval before it is called.
This is done with the following statement:

.. code-block:: c

	event_timeout.attach(event_callback_function, time_interval);

where event_callback_function is the name of the function to call,
and time_interval is expressed in milliseconds.
Time is counted from the moment when the callback function is attached.


Now look at the following code, and try it on your board.


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
tell you that onButtonStopDebouncing is not defined in the function onButtonPress,
which is correct, because it is defined further down in the code. 
But if you swap the order of the function, then the compiler will complain that
onButtonPress is not declared in onButtonStopDebouncing.

This is why we have to introduce an early declaration of
onButtonStopDebouncing
before we write the code of the function onButtonPress.
It tells the compiler what the function onButtonPress will be (types of parameters and output)
which is essentially the information needed to compile onButtonPress properly.






No time to waste!
-----------------

The solution above is very satisfactory.
We are not wasting time any more in the interrupts.
Having done this, it now looks like the code inside the main function is 
not optimal either;
we are still wasting time stuck during wait statements.
Maybe there is also a better way to blink a LED while allowing the processor
to focus on more important tasks?

Try the code below.
It uses the **Ticker** class, which calls a callback function at regular time intervals.
Essentially the whole program is now managed by interrupts.
We don't even need the while loop in the main function.


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

		// Even more important code could be placed here
		
	}


Note that the main function could still access the state of
the button or LEDs at any time. 


