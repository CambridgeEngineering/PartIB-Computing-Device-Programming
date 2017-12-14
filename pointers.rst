Pointers
========

This section provides some background information to better understand the techniques used in this activity.
You will find a lot more information online about memory management if you look, possible too much.
The paragraphs below are really highlighting the minimum required to not be surprised by the some the syntax used later.



Memory and address of variables
-------------------------------


You already know that a key difference between C/C++ and python is the fact that variables have a specific type and need to be declared.

When a variable is declared in a program:


.. code-block:: c

	int x;

The compiler allocates a region of the available memory to contain data of the type specified.
For an integer, it would allocate 4 bytes (32 bits) to store it.
Whenever you refer to x in your code, the compiler understands that you talk about the area in the memory that was created when you created the variable x.

When you write:

.. code-block:: c

	x=5;
	
The compiler writes in the block of memory associated with x the binary representation of the integer 5.

In general, you don't need to know where your variable is in memory; you just need to know it is called x.
In some occasions it is however convenient to be able to access the location in memory of a variable, its *address*.
In this section, we will discuss about how to manipulate variables (and functions) using their memory address.

Considering again the variable x, one can get its address using the notation &x.
The type of &x is not necessarily the same as x. It is called a *pointer*, which is defined as the type of an address.

You can declare pointers like other variables, although the syntax is a bit unusual.

.. code-block:: c

	int* p;
	p = &x;


Declares a pointer to an integer.
In a way, it does not really matter what types in points to, as all data is stored in the memory in the same way.
A pointer to an integer could in practice point to an area of memory where you stored a float or even some code.
The compiler uses this information to help you check consistency, but you could do horrible things with pointer if you don't use them properly.
The following code should for instance return an error:

.. code-block:: c

	float x;
	int* p;
	p = &x;


How to manipulate data stored at a particular address? 

While &x represents the address of x, *p represents the object stored at the address p.

.. code-block:: c

	int x;
	int* p;
	p=&x;
	*p=3;

Would store 3 in the variable x.


Why is this useful?
-------------------

In C/C++, when a function is called with parameters, the value of the parameters are passed, but not the variables themselves.
So the function cannot modify the values of its parameters.

Imagine you want to create a function that swaps the values of its parameters.
You could write something like this:

.. code-block:: c

	void swap(int a, int b)
	{
		int temp;
		temp=a;
		a=b;
		b=temp;
	}

But this would not work, because if you call swap(x,y), the variables a and b in the swap function would contain copies of the values of x and y, stored at different location in memory.

Pointers offer a way to solve this issue.
Look at the following code and try to understand what it does.


.. code-block:: c

	void swap(int* a, int* b)
	{
		int temp;
		temp=*a;
		*a=*b;
		*b=temp;
	}

Now instead of passing the values to the function, we provide the location of the data in memory.
The function can now manipulate the content stored in memory to achive the swap.

This function would work. To call it, we would need to pass the address of the variable instead of their values, i.e. call it with:

.. code-block:: c

	swap(&x, &y)

to swap the content of the variable x and y;



.. admonition:: Task

	**Write a function neg(x) such that it changes the sign of the variable x.
	Hint: it would be called using neg(&x)...**




Function pointers
-----------------

Pointers can also contain the address of a section of code, rather than data.
this is how one can pass a function as a parameter to another function... by passing the address of its code in memory.
We will use this to tell the microcontroller what to do (i.e. what code to execute) when particular events occur.

For now, let's just look at a typical situation where this would be useful.
Imagine that you want to find the second derivative of a function *f*.
To find a good numerical estimate, you have to calculate:

The algorithm is generic enough that you would like to allow it to work for any function.
This is a case where passing the function as a parameter is useful.


.. code-block:: c

	#include "mbed.h"

	Serial pc(SERIAL_TX, SERIAL_RX);

	float f_1(float x)
	{
		return(x-x*x);
	}


	float second_derivative( float (*f)(float), float x)
	{
		float h = 0.001;
		float d2fdx2;
		d2fdx2 = ( f(x-h) - 2 * f(x) + f(x+h) ) / (h*h);
		return d2fdx2;
	}


	int main() {
		pc.baud(9600);
		float x=1.;
		
		pc.printf("Function Pointer test program. \r\n");

		pc.printf("Function value: f(%f)=%f \r\n", x, f_1(x));

		pc.printf("Second derivative: %f \r\n", second_derivative(f_1, x));

	}

You can then calculate the second derivative of *f_1* for *x=1* using second_derivative(f_1, 1).

**Comment**: This example may look confusing if you are reading attentively enough.
Why didn't we pass the address of the function, using second_derivative(&f_1, 1)?
Why didn't we call the function f using (*f)(x) in the second_derivative function?

The reason is that a function name is treated by the compiler as a pointer.
You could also have used the following syntax for the calculation of the second derivative:

.. code-block:: c

	d2fdx2 = ( (*f)(x-h) - 2 * (*f)(x) + (*f)(x+h) ) / (h*h);

and for the function call:

.. code-block:: c

	second_derivative(&f_1, 1)



.. admonition:: Task

	**Start a new project with the code above, add a function called first_derivative
	to calculate the first derivative of a function using the approach above,
	and print both the first and second derivative of f(x)=x*x for x=2.**

	**Note that this requires you to get the text output
	as presented in the tutorial. If you can't get the text output to work at this stage, just skip
	this exercise for the moment.**




