Interrupts
==========


LED toggle
----------

Let's look at one of the previous tutorial 's example:


.. code-block:: c

    #include "mbed.h"
     
    DigitalIn button(USER_BUTTON);
    DigitalOut led2(LED2);
     
    int main()
    {
      led2=0;
      while(1)
      {
		led2= !(button == 0);
       wait(0.02); 
      }
    }



What if we would like now to toggle the state of the LED each time we
press the button: if the LED is off, pressing the button turns it on,
but if the LED is off, pressing the button turns in off.

Instinctively one would want to write the code below:


.. code-block:: c

	#include "mbed.h"
	 
	DigitalIn button(USER_BUTTON);
	DigitalOut led1(LED1);

	int main() 
	{
	  led1=0;
	  while(1) 
	  {
		if (button == 0) led1= !led1;
		wait(0.02); 
	  }
	}



But this doesn't really work.
Try it out, and try to find out why.

The problem is that the state of the LED keeps alternating as long as
the button is pressed. What we need to capture is not the state of the
button, but rather a particular
event: the change of state of the button.

How to solve this problem? 
You could increase the duration of the wait, hoping that the user would
release the button by the time it ends.
But then you may as well miss the input all together.

One could monitor carefully the state of the button, and
switch colour when the state changes.
It would be manageable for such a simple task, but hardly scalable for
complex interactions.
Luckily, micro controllers have
mechanisms to help you do this, either at the hardware level, or at a
low software level (kernel), such that the user doesn't need to worry
about it it.


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
		button.rise(&onButtonPress);  
		while(1) 
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

    button.rise(&onButtonPress);  

assigns a particular function with the "rise" event on the pin, which
corresponds here to the button being pressed (transition low to high on the pin).

The function onButtonPress is called a callback function.
It doesn't take any parameter, and doesn't return any either.
But it changes the state of the LED when the button is pressed.


Try the code and see what happens.

You will find that this somehow works, but it is still slightly random.
This is because the button is not perfect.
When you press it, its state can fluctuate for a short time, a process
called bouncing.

There are different ways to solve this problem.
Some involve hardware solutions, trying to prevent rapid oscillations for instance.
But here we are stuck with this button on the board.
So the way forward is to fix is with software.

We will see here a quick and dirty fix to show you that the issue is indeed related to
switch bouncing.
In the next section, we will discuss about proper solutions for this problem.

What we want is to prevent the onButtonPress function to be called multiple
times when the button is pressed.
To do this, we just need to force the program to wait a short time
after each call of the callback function.
This can be achieve by adding a wait function call in the callback function.

Try to change the code of the callback function to:

.. code-block:: c

	void onButtonPress() 
	{
		led1 = !led1;
		wait(0.3);
	}
 


You should find at this point that the toggle runs properly.
Hooray!


In the next section, we will explain why this solution is not really good practice,
and develop a more complex example that will show you the real power of interrupts.





