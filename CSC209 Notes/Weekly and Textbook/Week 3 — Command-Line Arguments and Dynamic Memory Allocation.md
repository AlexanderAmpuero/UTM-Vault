# Last Week Recap
1. `*` and `&` are *operators*
	1. `&` "returns" the address of any *named* variable.
	2. `*` dereferences any *address* (whether stored in a pointer or not)
2. **Only** for variable declaration, `*` serves to *identify* variables that are pointers
3. When reading/writing a pointer variable w/o dereferencing, you are reading/writing the address contained in the pointer.
# Command-Line Arguments
## Key Points
1. just like stdin, command-line arguments are another method of providing input to a program.
2. Use strtol() to parse strings containing integers
▶ More robust than other methods
▶ We don’t want segmentation faults when processing invalid input: Always terminate gracefully upon errors

## Casting Pointers
What does the following program print? 
```C
#include <stdio.h>
int main() {
	int x = 0x00616263;
	char *y = (char *)&x;
	printf("%s\n", y);
	return 0;
}
```

To understand this you must have an ASCII Table and understand hexadecimal
![[Pasted image 20260118212701.png]]
In memory, x looks like this:
`63 62 61 00` - Hexes
 `c  b  a \0` - Corresponding chars
 Where %s prints characters until it hits `\0` and `y` points to the first byte of `x`.
 Output: `cba`

## Local Variables
Local's are stored in the function's *stack frame*
- In `gdb`, `backtrace` prints list of stack frames, tracing from currently-executing functions up to `main()`
 When a function returns, its stack frame is deallocated
- The freed-up space of the stack can be reused by future function calls. 

## Global Variables
Globals variables are stored in another region of memory
- This includes read-only *string literals*.
These variables remain in memory for the entire duration that the program is running

# Dynamic Memory Allocation
Dynamically allocated variables:
- Put on the heap
- Remain allocated even after the allocating function returns

If you type in `info proc mappings` in `gdb` you can see print mapped memory regions!
![[Pasted image 20260118213652.png]]

## Dynamic Allocation in Java
```Java
ArrayList createArray() {
	ArrayList a = new ArrayList()
	return a;
}
```

## Dynamic Allocation in C
```C
int *createArray() {
	int *a = malloc(sizeof(int)*ARRAY_LEN);
	return a;
}
```

## Freeing Memory: Java v. C
Java *garbage collector* frees up memory when an object is no longer referenced by any variable.
In C, you have to collect your own garbage
- Use `free()` to free up allocated space that is no longer being used.
- Failure to do so results in *memory leaks*, which unnecessarily occupy space in memory.
- Use `valgrind` to detect memory leaks (Sasha note: `valgrind` in `gdb`?)

If a dynamically allocated variable finds a memory address which the programmer forgot to free, thats a memory leak. It can cause major and difficult errors in C code. 
# Intro to Strings in C
C strings are contiguous memory regions where the last character is `\0`, so you must include it.
```C
int main() {
	char s1[] = "Hello";
	char s2[209] = "World";
	char s3[7] = "CSC209";
	char s4[3] = {'U', 'T', 'M'}; // This is wrong!
	char s5[4] = {'U', 'T', 'M', '\0'};
	char *s6 = malloc((1000) * sizeof(char));
	strcpy(s6, "hello");
	printf("s1:%s|\ns2:%s|\ns3:%s|\n", s1, s2, s3);
	printf("s4:%s|\ns5:%s|\ns6:%s|\n", s4,s5, s6);
	return EXIT_SUCCESS;
}
```

# Extras
## Installing and Using `gdbgui`
```CMD
$ python3 -m pip install gdbgui
$ gcc -g -o myprog myprog.c
$ python3 -m gdbgui ./myprog
```

The first step isn't necessary on lab PCs since its already installed there. 
Instead of saying myprog, you insert your file name. 
Note: ADD MORE ABOUT THIS FROM LECTURE
