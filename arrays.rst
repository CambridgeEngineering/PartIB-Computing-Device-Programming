Arrays
======



Declaring arrays and accessing their elements
---------------------------------------------


Arrays in C/C++ are structures containing a set number **N** of elements of a given type **T**.
To declare an array *a* of 10 integer, one would write:


.. code-block:: c

	int a[10];

Elements in the array are indexed between 0 and N-1.
Brackets are used to access the content of each element.
For instance:

.. code-block:: c

	a[0]=2;
	x=a[0]+3;

The whole array can also be initiated at the point of declaration:


.. code-block:: c

	int a[5] = {0,1,2,3,4};



In memory, arrays are as contiguous sections of memory.



Passing arrays to functions
---------------------------


Because arrays can be large structures, arrays are not passed to functions by being copied.
In fact, the variable *a* declared above is de facto a pointer:
*a would return the first element of the array.

The following function would for instance set all the elements of an array to zero:

.. code-block:: c

	int data[5];
	
	void set_to_zero{int a[], n}
	{
		int i=0;
		while(i<n)
		{
			a[i]=0;
			i=i+1;
		}
	}

	main()
	{		
		set_to_zero{data, 5}
	}




Example
-------

Study the code below, guess what it would do and try it on your board.

.. code-block:: c

	#include "mbed.h"

	DigitalOut led1(LED1);
	DigitalOut led2(LED2);
	DigitalOut led3(LED3);

	const int N = 4;
	int led_cycle[N]={1,2,3,2};

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
			select_led(led_cycle[t]);
			wait(0.5);
			t=(t+1)%N;
		}
	}




.. admonition:: Task

   **Change the code to repeat the sequence {Red, Blue, Red, Green, Blue, Green}.**



Optional - Dynamic memory allocation
------------------------------------

This section of the tutorial is useful for the second variant of the activity.



