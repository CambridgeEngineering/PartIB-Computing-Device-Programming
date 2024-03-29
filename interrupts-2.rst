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
		wait_us(300000);
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


Callback functions need to be small
-----------------------------------

Focus now on what happens to the task running in the main function loop, flashing periodically the LED 3.
This loop stops for a little while when the button is pressed,
i.e. when the micro-controller is running the wait statement in the callback function.
Although we need this time delay to prevent bouncing, this also prevents the potentially important code running in the main function to be executed properly.
Large callback functions would also block other interrupts that may be required to handle additional events in a more
complex application. For this reason, it is not appropriate to include time consumming code in a callback function.

**It is a general rule to spend as little time as possible in interrupts. Commands such as wait, printf and communications with other ports including Serial or I2C, may cause your micro-controller to not behave properly. Allocating and reallocating memory should also be avoided within a callback function. The compiler may even refuse to compile a code that includes such time consumming tasks in the callback functions.**


What if you really would like to excute longer tasks in an interrupt? You will need to think about alternative ways to execute the time consuming tasks outside of the interrupt. For instance, you could introduce a global boolean variable that the interrupt sets to a particular value to indicate the interrupt code was executed. The main function loop may then monitor this variable and execute the relevant code when appropriate. 


Back to our LED toggle example... 
The wait statement is only here to prevent the button to trigger multiple
interrupts when pressed.
We could do this differently: get the callback function to deactivate the
button interrupt for a short time, and then call another function later on to turn it back on again.
During this time interval, we want of course the main function to continue its
important job of flashing the LED.

We could deactivate an interrupt with the following statement:

.. code-block:: c

	button.rise(NULL);

``NULL`` is a generic C/C++ constant.
When a pointer value is ``NULL``, it indicates that the pointer points to nothing.
The code line above therefore replaces the address of the callback function with a value
that unambiguously indicates that there is no call-back function to call.


But how to reattach the interrupt to the callback function after a while,
without using a ``wait`` statement?
Time to talk about timers and time interrupts.

Time interrupts
---------------

Time interrupts allows us to trigger a callback function after a given
amount of time.
Two main methods are available with mbed:

- **Timeout** if a callback function needs to be called only once after a time delay,

- **Ticker** if the callback function needs to be called at regular time intervals.


To use a time interrupt, we need to declare a variable of type ``Timeout``.

.. code-block:: c

	Timeout event_timeout;

We can now attach to the object ``event_timeout`` a callback function and indicate the time
interval before it is called.
This is done with the following statement:

.. code-block:: c

	event_timeout.attach(event_callback_function, time_interval);

where ``event_callback_function`` is the name of the function to call,
and ``time_interval`` is expressed in seconds.
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

This is because the functions ``onButtonStopDebouncing`` and ``onButtonPress``
call each other.

If you remove the first declaration of ``onButtonStopDebouncing``, the compiler will
tell you that ``onButtonStopDebouncing`` is not defined in the function ``onButtonPress``,
which is correct, because it is defined further down in the code. 
But if you swap the order of the function, then the compiler will complain that
``onButtonPress`` is not declared in ``onButtonStopDebouncing``.

This is why we have to introduce an early declaration of
``onButtonStopDebouncing``
before we write the code of the function ``onButtonPress``.
It tells the compiler what the function ``onButtonPress`` will be (types of parameters and output),
which is all the information it needs to compile ``onButtonPress`` properly.






No time to waste!
-----------------

The solution above is very satisfactory.
We are not wasting time any more in the interrupts.
Having done this, it now looks like the code inside the main function is 
not optimal either;
we are still wasting time stuck in a wait statements.
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
		button.rise(onButtonPress);  
		cycle_ticker.attach(onCycleTicker, cycle_time_interval);

		// Even more important code could be placed here
		
	}


Note that the ``main`` function could still access the state of
the button or LEDs at any time. 


