Activity 1: Memory and interupts
================================



*Material under development*



Brief
^^^^^



You have to program your microcontroller to record a sequence of colours
entered by the user, and then play it back. This has to be done using
your Nucleo boards only, i.e. using a push button and the three user LEDs
present on the board. Here is the approach:

- The board starts by cycling the three LEDs, turning on one of them at time for 1 sec and then switching to the next:
	Red on for 1s --> blue on for 1 sec --> green on for 1s --> red again, etc.

- The user selects a colour by pressing the button.
	The colour that is on at this time is recorded.

- The process continues until either:
	- Option 1: N colours have been entered (where N is defined in the code), or
	- Option 2: the user double-clicks the button to indicate the end of the sequence.

Option (i) is simpler, and is
the recommanded task for most students. Option (ii) is more challenging
and might be selected by more experienced (or keen) students.



.. raw:: html

   <iframe width="560" height="315" src="http://www.youtube.com/embed/NxvF4u7iIuE" frameborder="0" allowfullscreen></iframe>




Learning objectives
^^^^^^^^^^^^^^^^^^^

To complete the task, you may need to learn two important aspects of
low level programming:

- The record a sequence, a data structure is needed.
	In python, or during the Mars Lander exercise, you used clever data
	structures that could easily change size to accomodate more data.
	When programming simple devices, one tends to keep tighter control on
	memory, allocating buffers with a specific size. We will look at how
	simple arrays work in C and study examples to store and access data in them.

- User interactions, such as pressing a button, are events that need to be monitored.
	Micro-controllers have a mechanism for this called interupts, whereby you
	can attach specific actions to specific events. You will learn how to
	handle these interupts effectively.

- Talking about memory at a low level requires to learn about pointers.
	A pointer is essentially a number that corresponds to the location in memory
	of a particular data structure or function code.
	Pointers will be useful to tell interupts which function to call when
	the button is pressed, or to keep track of where the memory buffer containing
	your data is located in mamery.


Structure of the activity
^^^^^^^^^^^^^^^^^^^^^^^^^

Before getting started with the main task, you are invited to follow a
tutorial to learn about the prerequisote above.

This should give you the background knowledge needed to tackle the
option (i) of the task, where the number of colour to record is set in
the code iteself.

Students willing to try option (ii) will be given additional information
to tackle dynamic memory management.
If you don't know how many colour
to record, you will need to resize the buffer of memory when more data
storage is required.
We'll shpow you how to do that bofore you attempt the problem.




.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   pointers
   arrays
   interrupts
   interrupts-2
   project-1
   project-1-extension

