### Important Reminders
First graded lab due Friday
PCRS w3 — Dynamic Memory
Milestone 1 — Assignment — next Friday

# Visualizing and Debugging
pythontutor.com/c.html
Learn `gdb`, it will be useful later in the course.
Understand gdbgui.com, helps create debugging visualizations.
![[Pasted image 20260111201352.png]]

# Programming in C
## Return Values
`while (scanf(...) != EOF) {...}`
- Almost every library call has a return value
- Always check return values
	- C doesn't throw exceptions like Java and Python, it just fails
	- Be paranoid about whether or not each library calls completes successfully

What does the above code do? 
`scanf(...)` returns the number of input items assigned. In the event of a matching failure, it outputs 0, and if an input failure occurs, it returns EOF, stopping the loop from occurring.

## Macros
Return values are often defined as macros. ex. `EOF`
- These typically "expand" to integer constants
- Typically defined in `.h` files
- Already dealt with this during PCRS
![[Pasted image 20260111202450.png]]

You can replace every occurance of the word `DAYS` with `365` by using:
`#define DAYS 365`
You can do the same for True and False statements, and these Macros are extremely common.
`#define MAX_SIZE 100`

## Memory (un)Safety
C assumes that you know what you're doing.
- A perilous assumption: 70% of security vulnerabilities in Microsoft products are due to **avoidable mistakes that C/C++ allow you to make**.

Ex.
```C
int arr[10];
arr[-1] = 123;
```

Use gcc flag `-fsanatize=address` to catch memory safety bugs

Your code should be able to compile with the following:
```UNIX
-g 
-Wall # All warnings
-Wextra # Even more warnings
-Wpedantic # All possible errors warning
-Werror # Treat all warnings as errors

-fsanitize=address,leak,object-size,
bounds-strict,undefined
-fsanitize-address-use-after-scope
```
These flags make all warnings into errors and check several common memory errors

## Undefined Behaviour
Undefined behaviour is any operation for which the C standard imposes no requirements
Ex. The content of uninitialized variables are **undefined** 
- The following code will likely print **garbage values**, but **it will compile and run** without memory checks.

```C
int a;
printf("%d", a);
```

# PCRS
## Arrays
Declaring arrays:
`int student_ids[400];`
Writing to or reading from array elements:
`student_ids[0] = 1001111111;`
`student_ids[399] = 1002222222;`
`int x = student_ids[0];`

## Pointers and the & Operator
A pointer is a variable that contains the memory address of another variable

1. Assume `x` is an integer and `px` is a pointer
2. Then `px` = `&x` stores the address of `x` in `px`

The `&` operator expects its operand to be a variable or array element. So constructs such as 
`&(x+1)` are illegal.

## Pass-by-value and Pass-by-reference
```C
int x = 10;
increment_int(x); // Cannot change x
increment2_int(&x); // Can change x
```
`increment()` takes an *integer* parameter, whereas `increment2()` takes an address of an integer. 

## Pointers and the * Operator
The `*` operator interprets its operand as an address, and fetches the memory contents at that address.
1. Assume that `y` is an integer and `px` is a pointer
2. The statement `y = *px` assigns to `y` the integer that `px` points to

The `*` operator is said to `dereference` its operand.

## Declaring Pointers
Pointer declarations are intended as a mnemonic, so:
`int *px;` 
means that `*px` is equivalent to a variable of type `int`. Thus, `px` is a pointer containt the address of an `int`.

## Notes on Pointers and Addresses
▶ A pointer is not an array
▶ But it can contain the address of an array
▶ An array is not a pointer
▶ But the compiler interprets the name of an array as the address
of its zeroth element, so the following statements are equivalent

```C
int *x = &a[0];
int *y = a; // Assuming "a" is an array.
```

### Common Error
```C
int *x;
*x = 10;
```
```C
int *x;
printf("%d", *x);
```

The error in both of these is that we are **never** supposed to dereference uninitialized (or `NULL`) pointers.

# Compiler Warnings and Errors are your Friends
Common gcc compiler flags (all explained in man gdb):
	▶ -g: Include debugging symbols in compiled program (gdb and
	valgrind make use of these)
	▶ -Wall: Warn about highly-questionable code
	▶ -Wextra: More warnings (sometimes helpful)
	▶ -Wpedantic: All possible warnings
	▶ -Werror: Treat all warnings as errors


# Extras
## Common Size of C Primitives
![[Pasted image 20260111205303.png]]
GNU C compiler (gcc) default values (std=gnu11) on a 64-bit system. See GNU C Reference Manual.

Note: Compiler and machine dependent
## Pointer Size
On modern systems, pointers are 64 bits (8 bytes)
 ex., 0xFFFFFFFFFFFFFFFF
In memory diagrams, pay attention to whether each “cell” represents a single byte or multiple bytes
## Hexadecimal, Decimal, Octal, and Binary
A hexadecimal digit corresponds to 4 binary digits

0x prefix indicates hex, e.g., 0xFF
b prefix indicates binary, e.g., 0b11

You may also encounter octal notation
0 prefix, e.g., 012
\ prefix followed by up to 3 digits, e.g., \111

Try declaring int x and assigning values in hex, decimal, octal, and binary


