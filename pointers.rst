Pointers
========

This page provides background information to better understand the techniques used in this activity.
You will find a lot more information online about this topic online, possibly too much.
The paragraphs below are really highlighting the minimum required to not be surprised by the some the syntax used later.



Memory and address of variables
-------------------------------


You already know that a key difference between C/C++ and python is the fact that variables have a specific type and need to be declared.

When a variable is declared in a program:


.. code-block:: c

	int x;

the compiler allocates a region of the available memory to contain data of the type specified.
For an integer, it would allocate 4 bytes (32 bits) to store it.
Whenever you refer to x in your code, the compiler understands that you talk about the content stored in the area of memory that was allocated when you created the variable x.

When you write:

.. code-block:: c

	x=5;
	
the compiler writes in the block of memory associated with x the binary representation of the integer 5.

In general, you don't need to know where your variable is in memory; you just need to know it is called x.
In some occasions it is however convenient to know the location in memory of a variable, called its *address*.
In this section, we will discuss about how to manipulate variables (and functions) using their memory address.

Considering again the variable x, one can get its address using the notation &x.
The type of &x is generally not the same as the type of x.
An object storing an address of a particular data-type is called a *pointer*.

You can declare pointers like other variables, although the syntax is a bit unusual.
The code:

.. code-block:: c

	int* p;
	p = &x;


declares a pointer to an integer.
In principle, it does not really matter whether the pointers to an int or anything else; an address in memory is just a number regardless of what it points to.
A pointer to an integer could in practice point to an area of memory where you stored a float or even some code.
The compiler uses however this information to help you check consistency - you could do horrible things with pointer if you don't use them properly.
The following code should for instance return an error:

.. code-block:: c

	float x;
	int* p;
	p = &x;


How to manipulate data stored at a particular address? 

While &x represents the address of x, \*p represents the object stored at the address p.
The line:

.. code-block:: c

	*p=3;

would store 3 in the variable x.


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

But this would not work, because if you call swap(x,y), the variables a and b in the swap function would relate to new integer variables that contain copies of the values of x and y.

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
The function can now manipulate the content stored in memory to achieve the swap.

This function would work. To call it, we would need to pass the address of the variable instead of their values, i.e. call it with:

.. code-block:: c

	swap(&x, &y)

to swap the content of the variable x and y;



.. admonition:: Task

	**Without writing a full program and compiling it, think about how you would create a function neg(x) that changes the sign of the variable x.
	Hint: the function would be called using neg(&x)...**




Function pointers
-----------------

Pointers can also contain the address of a section of compiled code in memory, rather than data.
This allows us to pass a function as a parameter to another function, by passing the address of its code.
We will use this later to tell the microcontroller what to do (i.e. what code to execute) when particular events occur.

For now, let's just look at a typical situation where this would be useful.
Imagine that you want to find the second derivative of a function :math:`f`.
To find a good numerical estimate, you can use the `central finite difference
<https://en.wikipedia.org/wiki/Finite_difference_coefficient>`_ relationship that you studied in first year:

.. math::

   \frac{d^2f}{dx^2} = \frac{f(x-h) - 2 f(x) + f(x+h)}{h^2}

Your implementation is likely to be generic enough to be applied to any function.
Passing the function as a parameter is useful to make sure that such numerical methods
can be applied to any suitable function.

Study the code below and focus on the implementation of the ``second_derivative`` function.
It uses the USB Serial communication method introduced in the tutorial section
to output results, which is useful here to monitor what happens.



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



This example may look confusing if you are reading attentively enough.
Why didn't we pass the address of the function, using ``second_derivative(&f_1, 1)``?
Why didn't we call the function f using ``(*f)(x)`` in the second_derivative function?

The reason is that a function name is treated by the compiler as a pointer.
You could also have used the following syntax for the calculation of the second derivative:

.. code-block:: c

	d2fdx2 = ( (*f)(x-h) - 2 * (*f)(x) + (*f)(x+h) ) / (h*h);

and for the function call:

.. code-block:: c

	second_derivative(&f_1, 1)

but this is less readable.
Feel free to try it.

**Comment**: Note that we used again the printf function to display the output of the calculation.
The expression ``%f`` indicates that we want to insert there a float number.
All the parameters to be inserted in the output are additional parameters to the printf function.
Printf is a very powerful function to produce text output.
Feel free to explore further the range of `printf formatting options <https://www.cprogramming.com/tutorial/printf-format-strings.html>`_.

.. admonition:: Task

	**Start a new project with the code above, add a function called first_derivative
	to calculate the first derivative of a function using the approach above,
	and print both the first and second derivative of** :math:`f(x)=x^2` **for** :math:`x=2`.


.. math::

   \frac{df}{dx} = \frac{f(x+h) - f(x-h)}{2h}


**Note that this requires you to be able to read the text output
as presented in the debugging section. If you can't get the text output to work at this stage,
just think about what you would do but don't worry too much about executing the code.**




