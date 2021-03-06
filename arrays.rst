Arrays
======



Declaring arrays and accessing their elements
---------------------------------------------


Arrays in C/C++ are structures containing a set number **N** of elements of a given type **T**.
To declare an array **a** of 10 integers, one would write:


.. code-block:: c

	int a[10];

Elements in the array are indexed between 0 and N-1.
Brackets are used to access the content of each element.
Here are two examples:

.. code-block:: c

	a[0]=2;
	x=a[0]+3;

The whole array can also be initiated at the point of declaration:


.. code-block:: c

	int a[5] = {0,1,2,3,4};



In memory, arrays are contiguous sections of memory that are allocated to contain exactly
the amount of data requested.
Because all entries in an array are of the same type (and the same size in memory),
accessing the elements of an array is very fast:
to find the location in memory of the nth element, you add to the address
of the first element n times the size on an element.
To find the location of the next element, simply add the size of the element to the address current element.

If you try to access the content of an array beyond the range allocated,
expect to get random results, or a crash at execution time.

**Always double check what you are doing with arrays!**

The lack of reproducibility of the errors they generate makes debugging sometimes difficult.



Passing arrays to functions
---------------------------


Because arrays can be large structures, arrays are not passed to functions by being copied.
In fact, the variable **a** declared above is de facto a pointer.
The expression *a in your code would return the first element of the array.

The following function would for instance set all the elements of an array to zero:

.. code-block:: c

    int data[5];
    
    void set_to_zero(int* a, int n)
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
        set_to_zero(data, 5);
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


