Activity 1: Memory and interrupts
================================




Learning objectives
-------------------


This activity will teach you ways to catch user input, respond to it, and
record data based on these events.
These are very generic aspects of device programming.

For practical reasons, you will learn and practice these skills using the
embedded LEDs and button, which somehow constrains what can be done.
This activity however sets the foundations to handle more complex sensor data.


Task to complete
----------------


The aim is to program your microcontroller to record a sequence of colours
entered by the user, and then play it back.
Here is the proposed approach:

- The board starts by cycling the three LEDs, turning them on one at time, and switching every second:
	``LED1`` (green) for 1s --> ``LED2`` (blue) for 1 sec --> ``LED3`` (red) for 1s --> ``LED1`` for 1 sec, etc.

- While the colours are cycling, the user selects a colour by pressing the button.
	The colour that is ON at this time is recorded.

- The process continues until either:
	- **Option 1**: N colours have been entered (the size of the sequence N is set in the code), or
	- **Option 2**: the user double-clicks the button to indicate the end of the sequence.


- **Once recording is completed, the recorded sequence is played back on the LEDs.**

Option 1 is simpler, and is
the recommanded task for most students.
Option 2 is more challenging
and may be preferred by students who have more experience, and/or those who
want to extend develop their skills beyond what is required for this activity.



The video below presents a demo of the first option, so that you know what to aim for (https://www.youtube.com/embed/PDv8u4roZXs).

.. raw:: html

   <iframe width="560" height="315" src="https://www.youtube.com/embed/PDv8u4roZXs" frameborder="0" allowfullscreen></iframe>



What you may need to learn
--------------------------

To complete the task, you may need to learn a few important aspects of
low level programming:

- To record a sequence, a data structure is needed.
	In python, you can use clever data
	structures that could easily change size to accommodate more data.
	When programming simple devices, one tends to keep tighter control on
	memory, allocating buffers with a specific size. We will look at how
	simple arrays work in C and study examples to store and access data in them.

- User interactions, such as pressing a button, are events that need to be monitored.
	Micro-controllers have a mechanism for this called *interrupts*, whereby you
	can attach specific actions to specific events. You will learn how to
	handle these interrupts effectively.

- Talking about memory at a low level requires us to learn about pointers.
	A pointer is essentially a number that represents the location in memory
	of a particular data structure or function code.
	Pointers will be useful to tell interrupts which function to call when
	the button is pressed, or to keep track of where the memory buffer containing
	your data is located in memory.


Before getting started with the main task, you are invited to
learn about the prerequisites mentioned above.
Please take them in the right order.
This should give you the background knowledge needed to tackle the
activity.
It may take you 2-3 hours to go through it.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   pointers
   arrays
   dynamic_mem
   interrupts
   interrupts-2


The section on dynamic memory allocation is most relevant to students
tackling the second variant of the activity,
where you don't know at the start how many colours
to record in the sequence.











..   project-1
..   project-1-extension

