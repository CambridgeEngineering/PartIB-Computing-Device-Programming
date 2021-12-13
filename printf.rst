Printf
======

A brief note on the printf (print formatted) function.

Simple text output
^^^^^^^^^^^^^^^^^^

**printf** is a built-in function in what is known as the standard library.
 printf (print formatted) takes as input a format string and a series of variables.

The format string tells printf what to print and how it should appear.
A simple example is:

.. code-block:: c

	printf("test");

which will print the ASCII characters 't', 'e', 's' and 't'. The text "test" is shown in the terminal.

If we then call printf again with the same function argument ("test") we would observe "testtest" in the terminal.
This is why printf format strings often contain control characters such as \n or \r.
The backslash indicates to printf that the next character should be interpreted as a control character.
This is why:


.. code-block:: c

	printf("test\r\n");
	printf("test\r\n");

will result in the text "test" on one line, followed by the text "test" on the next line in the terminal.
The additional two control characters instruct the terminal to perform a carriage return followed by a linefeed.
This moves the cursor to the first position in the next line in the terminal.

Formatted output
^^^^^^^^^^^^^^^^



printf can also print out the values of variables.
To do this we need to provide printf with both a string and a series of variables we want to print out.
The string is a format string which instructs printf how to arrange the printout to include the values of the variables that have been provided.


.. code-block:: c

	printf("x: %d\r\n", x);

sends the string `x: %d\r\n` to `printf`.
The `%d` part in the string is a format instruction to `printf`.
It means substitute `%d` for an integer variable.
`printf` will read the contents of the variable `x` (which is an `int`) and splice it into the format string at the location of the formatting instruction.
For instance, if `x` is equal to 20 then printf will generate the string `"x: 20\r\n"`.

The code below prints out the contents of both x and y:

	#include "mbed.h"

	int main() {
	    int x = 20;
	    int y = 40;
	    printf("x: %d y: %d\r\n", x, y);
	}
	
Run the code on your Mbed device and ensure the terminal output is as expected. Using `printf` is very useful for quick debugging as it enables you to print out variable values at specific points in your code.
