# Introduction to Pointer in C++

##### Source: 
* MOOC **_CS101.2x Object-Oriented Programming_** by IIT Bombay

##### Table of Contents:
1. [Memory and Addresses in C++](#memory-and-addresses-in-c)
2. [Pointer as address of a variable](#pointer-as-address-of-a-variable)
3. [Access content at a given address](#access-content-at-a-given-address)
4. [C++ pointers in action](#c-pointers-in-action)
5. [Pointers in function calls](#pointers-in-function-calls)
6. [Pointers and dynamic memory](#pointers-and-dynamic-memory)

## Memory and Addresses in C++:

* Main memory is a sequence of physical storage locations
* Each location stores 1 byte (equivalent to 8 bits)
* Each location is uniquely identified by an address (in hexadecimal)
* When a program runs, the computer allocates a part of the memory to it, dividing into three parts:
 * Stack: call stack
 * Heap: dynamically allocated data
 * Code segment: store executable instructions

## Program variables:

```c++
int main() {
	int a;		// there will be 4 bytes for a in stack segment
				// each addressable memory location stores 1 byte
	float b;
	char c;
}
```

For example, when we declare an int variable (```a``` in this case), the program will allocate 4 bytes to store an int value in the memory, BUT only the address of the first byte in the memory would be used as the address of the variable. 

* a will have a location in the memory with the address range 0x00402-0x00405, but only 0x00402 is returned when we call
```c++
int *ptrA = &a;
```
(more on that later)

## Pointer as address of a variable:

How do we get an address of a variable?

```c++
int a;
int *p;
p = &a;
```

```&a``` is the address of ```a``` in memory.

In general, if ```x``` is a variable of type ```T```, T * is the type pointer to T and ```&x``` being an expression of type ```T *``` gives address of ```x```

_If the memory location of ```a``` spans from 0x00402 to 00x00405, what is the address of ```a```?_  
That would be 0x00402 because only the address of the first among bytes is the value of the pointer.

![memory](https://s31.postimg.org/4bh0zy27f/Screen_Shot_2016_07_20_at_12_12_29_AM.png)

According to the snapshot, the address of ptrA is 0x0040a and the content of ptrA, which is the address of ```a```, is 0x00402

### Can we have pointers to pointers?

```ptrA``` is a variable of type ```int``` (pointer to int), then ```&ptrA``` is the address of ```ptrA```, which is of type _**pointer to** pointer to int_

_Note: we don't write ```(int *) *```, but ```int **```_

We can take as far as we want. ```int ****``` means pointer to pointer to pointer to int

### A C++ program for printing addresses:

```c++
#include <iostream>

using namespace std;

int main() {
	int a;
	float b;
	char c;

	int *ptrA;
	float *ptrB;
	char *ptrC;

	ptrA = &a;
	ptrB = &b;
	ptrC = &c;

	cout << "Address of a is: " << ptrA;
	cout << "Address of b is: " << ptrB;
	cout << "Address of c is: " << ptrC;

	return 0;
}
```

## Access content at a given address:

C++ provides a _content of_ operator: *

If ```ptrA``` is a program variable of type ```int *```, ```*ptrA``` gives the integer stored at address given by ```ptrA```

```c++
int main() {
	int a;
	int *ptrA;

	a = 696969;
	ptrA = &a;
	cout << *ptrA;		// Prints the content of the address given by the value of ptrA
}
```

Since ```ptrA``` is a pointer to ```int```, its value is address of an _integer (4 bytes)_, so read 4 bytes starting from ```ptrA```

### Can we have dereferences of dereferences?

```c++
int main() {
	char c;
	char *ptrC;
	char **ptrPtrC;
	ptrC = &c;
	ptrPtrC = &ptrC;

	cin >> c;
	cout << *(*ptrPtrC);	// dereference of dereference

	return 0;
}
```

### How far can we nest dereferences?

* No pre-specified limit
* If x is a variable of type ```int ****```, we can use up to four levels of dereferencing of x


## Caveats when dereferencing:

* Certain memory addresses outside the part of memory allocated to process by system, and dereferencing such addresses leads to runtime error (segmentation violation)
* Memory location with address 0x0 is never within anyuser process' memory space
 * Dereferencing 0x0 will certainly cause program to crash

## C++ pointers in action:

```c++
#include <iostream>

using namespace std;

int main() {
	int m, n;
	int * ptrInt;
	int ** ptrPtrInt;
	
	ptrPtrInt = &ptrInt;
	
	cout << "Give m and n: "; cin >> m >> n;
	
	// Assigning ptrInt to the address of n
	ptrInt = &n; 
	cout << *(*ptrPtrInt) << endl;

	// Assigning ptrInt to the address of m
	ptrInt = &m; 
	cout << *(*ptrPtrInt) << endl;

	return 0;
}
```

```c++
#include<iostream>

using namespace std;

int main() {
	int m, n;
	int sum = 0;
	
	int *ptrInt;
	int *ptrSum;
	
	// Assigning ptrSum to the address of sum
	ptrSum = &sum;
	
	cout << "Give m and n : ";
	cin >> m >> n;
	
	// Assigning ptrInt to the address of n
	ptrInt = &n;
	// Update sum by dereferencing ptrSum and ptrInt and add the two together
	*ptrSum += *ptrInt;

	// Assigning ptrInt to the address of m
	ptrInt = &m;
	// Update sum by dereferencing ptrSum and ptrInt and add the two together
	*ptrSum += *ptrInt;
	
	// sum should be updated here
	cout << "Sum : " << sum << endl;
	
	return 0;
}
```

## Pointers in function calls:

There are two types of passing parameters to functions:
* Call-by-value
* Call-by-reference

### Passing pointers as function parameters:

When a function is called, there will be activation records in call stack, which help manage local variables, passsing of parameters and also flow of control

Passing pointers as function parameters will be call-by-value, but C++ allows passing references to pointers as well. References to pointer-valued ```(int *, char *, ...)``` variables treated in the same way as references to variables of other basic data types (```int```, ```char```,...)

```c++
#include <iostream>

using namespace std;

void swapByPtr(int *ptrX, int *ptrY);

int main() {
	int m, n;

	cout >> m >> n;

	swapByPtr(&m, &n);		// paramters are addresses, "call-by-value" with addresses

	cout << "m: " << m << endl;
	cout << "n: " << n << endl;

	return 0;
}

void swapByPtr(int *ptrX, int *ptrY) {
	int temp;
	temp = *ptrX;		// accessing contents of memory location in activation record of main from swapByPtr
	*ptrX = *ptrY;		// update contents of memory at address 0x749 with contents of memory at address 0x780
	*ptrY = temp;		// accessing contents of memory location in activation record of main from swapByPtr

	return;				// contents of m and n in activation record of main are swapped
}
```

##### Conclusion:

By passing pointers as function parameters, ```swapByPtr``` gets access to local variables of ```main```  
Another way to share variables between caller and callee is passing parameters by reference.  
In fact, when we pass parameters by reference in C++, after compilations pointers to parameters are actually passed. This will save us some untidy coding.

### Passing by reference:

```c++
#include <iostream>

using namespace std;

void swapByPtr(int *ptrX, int *ptrY);

int main() {
	int m, n;

	cout >> m >> n;

	swapByPtr(&m, &n);		// paramters are addresses, "call-by-value" with addresses
	swapbyRef(m, n); 		// passing by references

	cout << "m: " << m << endl;
	cout << "n: " << n << endl;

	return 0;
}

void swapByRef(int &x, int &y) {
	int temp;
	temp = x;
	x = y;
	y = temp;

	return;
}
```
Let's compare:

```c++									
void swapByPtr(int *ptrX, int *ptrY) {	
	int temp;							
	temp = *ptrX;	
	*ptrX = *ptrY;	
	*ptrY = temp;		
						
	return;				
}

void swapByRef(int &x, int &y) {
	int temp;
	temp = x;
	x = y;
	y = temp;

	return;
}
```

##### Conclusion:

```swapByRef``` is cleaner to use.

### Can a function return a pointer?

Yes, but be careful so that the returned pointer does not point to a location in activation record of the function, because the activation record is freed when a function returns and dereferencing such an address in the freed activation record will cause the program to crash.

Example of a function that returns an invalid pointer:

```c++
int *myFunc(int *ptrB);

int main() {
	int *a, b;
	cout << "Give b: ";
	cin >> b;
	a = myFunc(&b);					// address of local variable in non-existent activation record of myFunc: BAD ADDRESS
	cout << "a is: " << *a << endl;	// dereferencing a BAD ADDRESS

	return 0;
}

int *myFunc(int *ptrB) {
	int a;					// local variable in activation record of myFunc
	a = (*ptrB)*(*ptrB);	// address of local variable in activation record of my Func
	return &a;
}
```

The correct way would be:

```c++
int *myFunc(int *ptrB) {
	int a;					// local variable in activation record of myFunc
	a = (*ptrB)*(*ptrB);
	*ptrB = a;

	return (ptrB);			// address of variable in activation record of main
}
```

## Pointers and Dynamic Memory:

### Overview:

* Operating system allocates a part of main memory for use by a process
* Divided into:
 * **Code segment**: stores executable instructions in program
 * **Data segment**: For dynamically allocated data
 * **Stack segment**: call stack

Local variables/arrays of a function must be statically declared in function. Their memory is allocated in activation record of function and resides in the stack segment. However, they cease to exist once function ends and controls returns to the caller (i.e. main).

One question arises: what if the size of a local array is input-dependent? For example, we read the number of students in a calss and store marks of all students in an ```int``` array. Or what if memory location(s) allocated in a function must be accessed after the function returns?

Therefore, we need a mechanism for allocating memory allocations **dynamically**. Features of dynamic memory allocation:
* Allocation at runtime
* Allocation could depend on values of expressions read/computed

Let's look at some code:

```c++
int main() {
	int numStudents;
	cin >> numStudents;

	// Allocate an int array A
	// of size numStudents
	// to store quiz marks
}
```

We can see that ```int numStudents``` is known at compile-time, but the array ```A``` is unknown at compile time, so the program cannot reserve space for array ```A``` when activation record of ```main``` is created in stack segment. So... where should this memory space come from?

It turns out that C++ provides a special construct for dynamically allocating memory in heap (data segment)

### Dynamica memory allocation in C++:

C++ provides a special construct for dynamically allocating memory in heap (data segment):

```c++
int main() {
	int numStudents;
	int *A;
	
	cin >> numStudents;
	A = new int[numStudents];

	// Store quiz marks in A

	return 0;
}
```

![dynamic memory](https://s31.postimg.org/oggp481t7/Screen_Shot_2016_07_24_at_3_38_58_PM.png)

Then,

```c++
A[0] = 10;
A[1] = 15;
```

So how are addresses of A[0] and A[1] calculated?

The compiler knows that A is pointer to an integer that is the first in an array of integers from ```A = new int[numStudents];```. Each element of the array is of ```int``` data type and each ```int``` takes 4 consecutive memory allocations (bytes). Therefore, address of ```A[0]``` is (A + 0) and that of ```A[i]``` is (A + (4*i)).

To dynamically allocate memory for a variable of type T:

```c++
T *myVarPtr;
myVarPtr = new T;
```

The variable is then accessed as ```*myVarPtr```.

To dynamically allocate memory for an array of n elements of type T:

```c++
T *myArray;
myArray = new T[n];
```

The ```new``` keyword returns to pointer to T in both cases. Array of type T treated as a variable of type T* can be indexed using [...].  
Most often, ```new``` will successfully allocate requested memory from heap and return address to the first allocated byte. However, we cannot take ```new``` for granted because in some cases 0x0 (also called NULL pointer) may be returned. Therefore, we should always check if ```new``` has returned an address other than 0x0 (NULL) before dereferencing that address.

```c++
int main() {
	int numStudents;
	int *A;

	cin >> numStudents;
	A = new int[numStudents];

	if (A != NULL) {
		A[0] = 10;
		A[1] = 15;
	}

	return 0;
}
```

### Dynamic memory in action:

```c++
#include <iostream>

using namespace std;

int *readQuizMarks(int n);

int main() {
	int numStudents;
	int *qMarks;

	cout << "Given student count: " << endl;
	cin >> numStudents;
	cout << "Give marks of students " << endl;

	qMarks = readQuizMarks(numStudents);

	if (qMarks == NULL) {
		return -1;
	}

	int min = qMarks[0];
	int max = qMarks[0];

	for (int i = 1; i < numStudents; i++) {
		if (qMarks[i] < min) {
			min = qMarks[i];
		}

		if (qMarks[i] > max) {
			max = qMarks[i];
		}
	}

	cout << "Min: " << min << endl;
	cout << "Max: " << max << endl;

	return 0;
}

int *readQuizMarks(int n) {
	int *marks;
	int *temp;

	marks = new int[n];
	temp = new int[n];

	if (marks == NULL || temp == NULL) {
		return NULL;
	}

	for (int i = 0; i < n; i++) {
		cin >> marks[i];
		temp[i] = marks[i] + 10;
	}

	if (temp != NULL) {
		delete[] temp;
	}

	return marks;
}
```

### Deallocation of dynamic memory:

Memory dynamically allocated by a function is **not automatically de-allocated (freed)** when the function returns. Unless explicitly de-allocated, dynamically allocated memory persists until program ends execution. C++ provides a special construct to explicitly de-allocate dynamically allocated memory:

```c++
// For variables:
// Allocation:
T *myVar;
myVar = new T;
// Deallocation:
delete myVar;

// For arrays:
// Allocation:
T *myArray;
myArray = new T[n];
// Deallocation:
delete[] myArray;
```

Note:  
* Always deallocate dynamically allocated memory once you have no further use of it
* Check for valid address before deallocating memory
```c++
if (myArray != NULL) {
	delete[] myArray;
}
```
* Deallocating memory at address 0x0 (NULL) will cause program to crash.