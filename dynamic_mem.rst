Optional - Dynamic memory allocation
====================================

This section of the tutorial is useful for the second variant of the activity.
It presents more advanced features of memory management and data manipulation
using pointers.


**Memory allocation during program execution**


In C and C++ we can request memory from the system.
This is achieved using the functions malloc, realloc and free.
The function malloc (memory allocation) takes as input the size in bytes you wish to allocate in memory and returns a pointer of type void * which points to the first byte of memory:

.. code-block:: c

	void *p = malloc(10); // Allocates ten bytes; p points to the first byte
	
If the memory allocation request would fail (for instance, due to no memory being available), then the pointer p is set to NULL.
It is good practice to check that the returned pointer is not NULL to verify that the memory allocation indeed succeeded.


**Memory reallocation**



If we already have some memory allocated, then we can grow it with the function realloc (reallocate).
realloc takes as input a pointer to the first byte of an existing memory allocation and a desired new size of this memory allocation and returns a pointer of type void * which points to the first byte of the new memory allocation:

.. code-block:: c

	void *np = realloc(p, 20);

The above function call takes our old memory allocation pointed to by the pointer p and request its new size to be 20 bytes.
The new pointer np points to the first byte of this new memory allocation.
The old content is preserved in the new memory allocation (up to the new size of the new memory allocation).
Note that the old pointer p now points to an invalid memory area and is useless.
This pointer is often set to NULL to signal that it does no longer point to anything useful:

.. code-block:: c

	p = NULL;
	
This is known as a null-pointer. If p was not set to NULL it would be known as a dangling pointer.
These are very dangerous as any attempt to dereference them or use them in any other way results in undefined behaviour (possibly a crash, such as a segmentation fault).


**Freeing allocated memory**

When we are done using the memory we need to return it to the system.
Failing to do so means we have introduced a memory leak.
To free the new pointer np we use the function free and then set the pointer to NULL:

.. code-block:: c

	free(np);
	np = NULL;

Note that only dynamically allocated memory can be modified with realloc.
The functions malloc, realloc and free are all designed to work with dynamically allocated memory.
Memory allocations you did not create must not be passed to realloc or free as this results in undefined behaviour.


**Checking successful memory allocation**

realloc can fail to reallocate memory, for example due to no memory being available.
It is good practice to therefore verify that the returned pointer is not NULL.
If realloc fails then the original pointer still points to a valid memory allocation and will need to be freed manually.
A common pattern is therefore as follows:

.. code-block:: c

	void *np = realloc(p, 20);
	if (np == NULL) {
		free(p);
		// Insert code to handle memory allocation failure...
	}

To use memory allocation in practice, it is useful to transform the pointer of type void * to something more useful.
For example, if we want to allocate memory for storing integers then we would like the pointer to be of type int \*.
We would also like the memory to be allocated so that a particular number of integers fit within the allocated memory area.

Therefore, in practice, to allocate memory for 8 integers we would write:

.. code-block:: c

	int *p = (int *)malloc(sizeof(int)*8);
	
The sizeof operator returns the size of type int (4 bytes on your Mbed device), which we then multiply by 8 to get the total number of bytes we need to allocate from the system.

The (int *) part before malloc is known as *casting*.
Just like in a theatre play, where an actor can be recast into many roles, a return value or variable can be recast into a different type.
The casting operator changes the return type of malloc from void * (a pointer to any type) to int * (a pointer to an integer).

Having allocated the memory and cast the returned pointer to a pointer of type int \*, we can now read and write to the memory area by dereferencing the pointer and using pointer arithmetic:


.. code-block:: c

	int *xp = p;
	*xp = 16; Set first integer in the memory area to 16
	xp++; // Proceed to the next integer in the allocated memory area
	*xp = 32; // Set the second integer in the memory area to 32

We can also use array operations on our newly allocated memory area, as arrays in C and C++ are essentially the same as allocated memory areas with a fixed type (note however that pointers to arrays cannot be modified by calling malloc, realloc and free as they are not dynamically allocated by the system):

.. code-block:: c

	printf("First integer: %d"\r\n", p[0]);
	printf("Second integer: %d"\r\n", p[1]);
	
Here is a complete program that demonstrates the above concepts:


.. code-block:: c

	#include "mbed.h"

	int main()
	{
		int *p = (int *)malloc(sizeof(int)*4);
		int *xp = p;
		*xp = 2;
		xp++;
		*xp = 4;
		xp++;
		*xp = 8;
		xp++;
		*xp = 16;
		
		for (int i = 0; i < 4; i++) {
			printf("[%d]: %d\r\n", i, p[i]);
		}
		
		int *np = (int *)realloc(p, sizeof(int)*16);
		if (np == NULL) {
			free(p);
			printf("Memory allocation failure!\r\n");
		}
		else {
			for (int i = 4; i < 16; i++) {
				np[i] = 255;
			}
			for (int i = 0; i < 16; i++) {
				printf("[%d]: %d\r\n", i, np[i]);
			}
			free(np);
		}
	}
