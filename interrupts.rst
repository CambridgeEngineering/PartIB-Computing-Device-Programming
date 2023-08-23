Interrupts
==========


LED toggle
----------

Let's look at one of the previous tutorial's examples:


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
        wait(0.02); 
      }
    }



What if we would like now to toggle the state of the LED each time we
press the button: if the LED is off, pressing the button turns it on,
but if the LED is on, pressing the button turns it off.

Instinctively one would want to write the code below:


.. code-block:: c

	#include "mbed.h"
	 
	DigitalIn button(USER_BUTTON);
	DigitalOut led1(LED1);

	int main() 
	{
	  led1=0;
	  while(true) 
	  {
		if (button == 1)
			 led1 = ! led1;
			 // The symbol ! is the logical NOT
 		wait(0.02); 
	  }
	}



But this doesn't really work.
Try it out, and try to find out why.

The problem is that the state of the LED keeps alternating as long as
the button is pressed. What we need to capture is not the *state* of the
button, but a particular
*event*: the change of state of the button when it is pressed.

**How to solve this problem?**


You could increase the duration of the wait, hoping that the user would
release the button by the time it ends.
But then you might miss the input all together.

One could monitor carefully the state of the button, and
switch colour when the state changes.
It would be manageable for such a simple task, but hardly scalable for
complex interactions.
Luckily, micro-controllers have
mechanisms to help you do this, either at the hardware level, or at a
low software level (kernel), such that the user doesn't need to worry
about it.


Pin interrupts
--------------

Let's look at the code:


.. code-block:: c

	#include "mbed.h"
	 
	InterruptIn button(USER_BUTTON);

	DigitalOut led1(LED1);

	// Callback function to associate with the press button event
	void onButtonPress() 
	{
		led1 = !led1;
	}
	 
	int main() 
	{   // attach the address of the callback function to the rising edge
		button.rise(onButtonPress);  
		while(true) 
		{   // You could do something useful here
		}
	}


As you can see, this looks simple enough!
The line:

.. code-block:: c

	InterruptIn button(USER_BUTTON);

creates an object of type InterruptIn that gives you a handle to
monitor events on the pin ``USER_BUTTON``.

The line:

.. code-block:: c

    button.rise(onButtonPress);  

assigns a particular function with the "rise" event on the pin, which
corresponds here to the button being pressed.
It may appears slightly counter intuitive that the event is called
rise when you are pushing the button down... but this refers to the
fact that the input (voltage) on the corresponding pin is transitioning
from O to 1 (Vcc).

The function ``onButtonPress`` is called a *callback function*.
It doesn't take any parameter, and doesn't return anything either.
But it changes the state of the LED when the button is pressed.


Try the code and see what happens.

You will find that this somehow works, but it is still slightly random.
This is because the button is not perfect.
When you press it, its state can fluctuate for a short time, a process
called *bouncing* (https://www.youtube.com/embed/hAVQpKVck9s).


.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/hAVQpKVck9s" frameborder="0" allowfullscreen></iframe>





Mechanical switches and debouncing
----------------------------------

Bouncing is a common problem.
There are different ways to solve this issue.
Some involve hardware solutions, trying to prevent rapid oscillations
for instance using low pass filters.
But here we are stuck with this button on the board...
So the way forward is to fix it with software, another common approach.

We will see here a quick and dirty fix to confirm that the issue is indeed related to
switch bouncing.
In the next section, we will discuss proper solutions to this problem.

What we want is to prevent the `onButtonPress` function to be called multiple
times when the button is pressed and its state fluctuates for a
little while.
To do this, we just need to force the program to wait a short time
after each call of the callback function.
This can be achieve by adding a wait function call in the callback function.

Because it is not good practice to add a wait call in an interrupt (interrupt calls should execute fast),
the latest mbed compilers only allow us to use the `wait_us` function.
Here, "us" stands for microseconds, which is the unit of the duration to be passed as parameter.
We will see in the next tutorial how to use properly interrupts without relying on the wait functions.

Try to change the code of the callback function to:

.. code-block:: c

	void onButtonPress() 
	{
		led1 = !led1;
		wait_us(300000);
	}
 


You should find at this point that the toggle behaves properly.
Hooray!


In the next section, we will explain why this solution is not good practice,
and develop a more complex example that will show you how to properly
use interrupts.





