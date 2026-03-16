# PCRS
## Bit Manipulation and Flags
### Bitwise Operations in C
In C, bitwise operators allow for the direct manipulation of individual bits within a variable, ignoring the high-level data type to work with raw binary data.
#### 1. Logical vs. Bitwise Operators
Logical operators (`&&`, `||`) evaluate the "truthiness" of a whole value. Bitwise operators perform calculations on each corresponding pair of bits.

| **Operation** | **Bitwise Operator** | **Description**                                       |
| ------------- | -------------------- | ----------------------------------------------------- |
| **AND**       | `&`                  | Result is 1 only if **both** bits are 1.              |
| **OR**        | `\|`                 | Result is 1 if **at least one** bit is 1.             |
| **XOR**       | `^`                  | Result is 1 if bits are **different** (one 0, one 1). |
| **NOT**       | `~`                  | Unary operator; flips all 0s to 1s and 1s to 0s.      |

When evaluating logical operators we see for 
```C
int x, y;  
int a = 2;  
int b = 8;  
  
x = a | b;  
y = a || b;
```

`a | b` evaluates to 10, since a = 2 = 0000 0010 and b = 8 = 0000 1000
Thus *a or b* becomes 0000 1010 (which is 10)
a || b evaluates to 1, since 2 || 8 is true since they are both non-zero integers

---
#### 2. XOR as "Conditional Negation"

The XOR (`^`) operator acts as a toggle:

- If the first operand is **1**, the second operand is negated (flipped).
    
- If the first operand is **0**, the second operand remains unchanged.
    

#### 3. Bitwise NOT (`~`) and Integer Size

Negating a value flips every bit in its memory representation.

> [!WARNING]
> 
> On a 32-bit machine, `~0` does not equal `1`. It results in thirty-two `1`s (representing `-1` in two's complement), because the operator affects the entire width of the integer.

---
#### 4. Constants and Notation
To make bit manipulation readable, C (and `gcc`) supports different bases:
- **Binary:** Prefixed with `0b` (e.g., `0b00010011`).
- **Hexadecimal:** Prefixed with `0x` (e.g., `0x13`).
    - **Tip:** Hex is preferred because one digit represents exactly **4 bits** (a nibble), making translation to binary easy.

---
#### 5. Coding Example
```C
unsigned char a = 0x13; // 0001 0011
unsigned char b = 0x0F; // 0000 1111

// a & b (AND)
//   0001 0011
// & 0000 1111
// -----------
//   0000 0011 -> 0x03
```
### Bit Manipulation: Shifting and Idioms

Beyond basic operations, C provides **shift operators** to move bits within a variable. These are essential for creating "masks" to manipulate or check specific bits.
#### 1. Bit Shifting Operators

Think of shift operators as arrows pointing in the direction the bits will move.
- **Left Shift (`<<`):** Shifts bits to the left. The leftmost bits are discarded, and the rightmost bits are filled with **0**.
    
    - **Mathematical Effect:** Shifting a number one bit to the left is equivalent to **multiplying by 2**.
        
- **Right Shift (`>>`):** Shifts bits to the right. The rightmost bits are discarded.
    
    - **Mathematical Effect:** Shifting a number to the right is equivalent to **integer division by 2**.
---
#### 2. Common Bitwise Idioms
In C, bit positions are counted from right to left, starting at **0**. To manipulate an arbitrary bit ($k$), we create a mask using `(1 << k)`.

##### **A. Setting the $k^{th}$ Bit to 1**

To set a bit without changing others, use the **Bitwise OR (`|`)** operator. Since `1 | 0 = 1` and `1 | 1 = 1`, the target bit becomes 1 regardless of its original state.

- **Formula:** `var = var | (1 << k);`
    
- **Example:** To set the $3^{rd}$ bit (value 8), OR the variable with $00001000$.
#### **B. Checking/Testing the $k^{th}$ Bit**

To see if a specific bit is 1, use the **Bitwise AND (`&`)** operator. If the result is non-zero, the bit was 1; if the result is 0, the bit was 0.

- **Formula:** `result = var & (1 << k);`
    
- **Example:** To check the $2^{nd}$ bit (value 4), AND the variable with $00000100$.
---
#### 3. Summary Table: Bit Manipulation Goals

| **Goal**              | **Operator** | **Logic**               | **Programming Idiom** |
| --------------------- | ------------ | ----------------------- | --------------------- |
| **Set a bit**         | `\|`         | `1` forces the bit to 1 | `var \| (1 << k)`     |
| **Check a bit**       | `&`          | Returns `0` if bit is 0 | `var & (1 << k)`      |
| **Multiply by $2^n$** | `<<`         | Shift left $n$ times    | `var << n`            |
| **Divide by $2^n$**   | `>>`         | Shift right $n$ times   | `var >> n`            |
### Flag Bits and File Permissions
System calls often use **flag bits** to transmit multiple options through a single argument. This treats a variable (like a 32-bit `unsigned int`) as an array of bits where each bit acts as an on/off switch.

#### Linux File Permissions

Linux represents permissions using a 9-bit string for the **Owner**, **Group**, and **Others**. Each category has three bits: **Read (r)**, **Write (w)**, and **Execute (x)**.

- **Memory Layout:** These 9 bits are stored in the low-order (rightmost) positions of a `mode_t` variable.
    
- **Constants:** C provides octal constants (e.g., `S_IRUSR`) to simplify building these values. Octal is used because one octal digit corresponds exactly to three binary bits.
#### Common Operations

- **Setting Permissions:** Use **Bitwise OR (`|`)** to combine constants and "turn on" specific flags.
    - _Example:_ `mode = S_IRUSR | S_IRGRP;` (Sets read for user and group).
- **Checking Permissions:** Use **Bitwise AND (`&`)** to verify if a specific bit is set.

Flag bits are highly efficient, saving space while allowing for fast processing via bitwise operators.

```C
// 1. Create a mode using flag constants 
// S_IRUSR: Read for User, S_IWUSR: Write for User, S_IRGRP: Read for Group 
mode_t my_permissions = S_IRUSR | S_IWUSR | S_IRGRP; 
// 2. Apply permissions to a file 
if (chmod("example.txt", my_permissions) == -1) {
	perror("chmod failed"); 
	return 1; 
	
} 

// 3. Checking if a specific flag is set using bitwise AND 
if (my_permissions & S_IRUSR) {
	printf("User has read permissions.\n"); 
} 
return 0;
```

### Bit Flags as a "Set" Data Structure

Bit flags can implement a **set** where each bit represents the presence (1) or absence (0) of a positive integer. This is a compact, high-performance technique called **bit masking**.

#### 1. Basic Set Operations
- **Add an element ($k$):** Create a mask by shifting 1 left by $k$ positions and use **Bitwise OR (`|`)**.
    
- **Remove an element ($k$):** Create a mask where only the $k^{th}$ bit is 0 (by negating a shifted 1) and use **Bitwise AND (`&`)**.
    
- **Check an element ($k$):** Use **Bitwise AND** to see if the bit is non-zero.
- 

#### 2. Scaling with Large Sets
To store values beyond the 32-bit limit of a single integer, use an **array of integers**. To find bit index $k$:

- **Array Index:** $k / 32$ (integer division).
- **Bit Position:** $k \% 32$ (modulo).

#### 3. Implementation in C
Wrapping the array in a `struct` hides implementation details and allows easy set copying via assignment.
```C
typedef struct {
    unsigned int bits[N];
} BitSet;

void set(BitSet *s, int k) {
    s->bits[k / 32] |= (1 << (k % 32)); // Set bit to 1
}

void unset(BitSet *s, int k) {
    s->bits[k / 32] &= ~(1 << (k % 32)); // Set bit to 0
}

int is_set(BitSet *s, int k) {
    return s->bits[k / 32] & (1 << (k % 32)); // Check if non-zero
}
```

## Pipes

The standard C library uses `FILE` objects (like `fopen`, `printf`) which wrap low-level system calls to provide **buffering**. Buffering collects data into blocks, reducing the frequency of expensive system calls and improving performance. However, for interprocess communication, we must often interact directly with the underlying **file descriptors**.
### 1. File Descriptors and System Calls

A **file descriptor** is an integer acting as an index into a process's open file table. By default, every process has three descriptors open:

- **0**: Standard Input (`stdin`)
- **1**: Standard Output (`stdout`)
- **2**: Standard Error (`stderr`)

While high-level functions like `fprintf` are convenient, the `strace` utility reveals they ultimately trigger the `write` system call, using these integers to identify the destination.

---
### 2. Creating and Using Pipes
A **pipe** is a unidirectional communication channel used to send data between related processes.
- **Creation**: The `pipe(int fd[2])` system call creates a queue in the kernel and returns two descriptors.
    
    - `fd[0]` is for **reading**.
    - `fd[1]` is for **writing**.
        
- **Inheritance**: When a process calls `fork()`, the child inherits copies of the parent’s file descriptors. This allows them to share the same pipe.
    
- **Cleanup**: To establish a clear data flow, each process must close the end of the pipe it is not using.
    
    - If the parent writes to the child, the parent closes `fd[0]` and the child closes `fd[1]`.
    - **Crucial Step**: Closing the write end is vital; when all write descriptors are closed, `read()` returns `0`, signaling the end of data (EOF) to the consumer.

---
### 3. The Producer-Consumer Problem
Pipes solve the **Producer-Consumer** synchronization problem via the operating system:

- **Empty Pipe**: If a consumer (reader) tries to read from an empty pipe, the OS **blocks** the `read()` call until data is available.
    
- **Full Pipe**: If a producer (writer) tries to write to a full pipe, the OS **blocks** the `write()` call until the consumer creates space.

---
### 4. Redirection with `dup2`
The `dup2(oldfd, newfd)` system call copies an existing file descriptor to a specific index in the table. This is how shells implement redirection (e.g., `grep > file.txt`).
**Redirection Workflow**:

1. `fork()` a child process.
2. In the child, `open()` the target file.
3. Use `dup2(filefd, 1)` to redirect `stdout` to the file.
4. `close()` the original `filefd` to prevent leaks.
5. `exec()` the new program; it will now write to the file instead of the console.

---
### 5. Implementing the Shell Pipe Operator (`|`)

To connect two programs (e.g., `sort | uniq`), the shell combines `pipe`, `fork`, and `dup2`:

|**Process**|**Action**|**Goal**|
|---|---|---|
|**Parent (Producer)**|`dup2(fd[1], 1)`|Redirect `stdout` to pipe's write end.|
||`close(fd[0])`|Close unused read end.|
|**Child (Consumer)**|`dup2(fd[0], 0)`|Redirect `stdin` to pipe's read end.|
||`close(fd[1])`|Close unused write end.|

Once redirected, both processes call `exec()`. The first program's output flows directly into the second program's input through the kernel-managed pipe.

# Lecture 7
## Bitwise Operations
Working with file descriptors (FDs) and general low-level interfaces require us to be familier with bit operations.
1. Shift `>>` and `<<` 
2. Bitwise (`AND`, `OR`, `NOT`, and `XOR`)
3. Using bitwise operations to *set* and *clear* bits

### Bitwise Left/Right Shift Operator
`i << j` shifts bits in `i` to the **left** by `j` places
- The right-hand-side is filled w/ 0 bits if `i` *positive*
	- It is undefined if `i` is *negative*
```C
unsigned char i, j;
i = 13; /* binary 00001101 */
j = i << 2; /* binary 00110100 */
```

`i >> j` shifts its in `i` to the right by `j` places
- ...
```C
unsigned char i, j;
i = 13; /* binary 00001101 */
j = i >> 2; /* binary 00000011 */

char k, l;
k = -128; /* binary 10000000 */
l = k >> 2; /* binary 11100000 (maybe) */
```
What does maybe mean?? Ask in lecture

### Bitwise AND
```C
char i, j, k;
i = 21; /* binary 00010101 */
j = 56; /* binary 00111000 */
k = i & j; /* binary 00010000 */
```
The `AND` operator only returns the binary values they share

### Bitwise OR
```C
char i, j, k;
i = 21; /* binary 00010101 */
j = 56; /* binary 00111000 */
k = i | j; /* binary 00111101 */
```
The overlap of both binary values

### Bitwise NOT
```C
char i, j;
i = 21; /* binary 00010101 */
j = ~i; /* binary 11101010 */
```
The inverse of the binary value

### Bitwise XOR
```C
char i, j, k;
i = 21; /* binary 00010101 */
j = 56; /* binary 00111000 */
k = i ˆ j; /* binary 00101101 */
```
Outputs true only when inputs are different

## Convention for Numbering Bits
Convention for referring to specific bits is to number them from the right-hand-side (ex. a char consists of 8 bits, starting from `b0` on the right most bit)
`b7 b6 b5 b4 b3 b2 b1 b0`

### Setting Bits
To set bit 4 of integer `n` (i.e. set value of bit 4 to 1)
`n = n | (1 << 4)`
or (16 is less clear though since we're converting the bit to integer)
`n = n | 16`
or
`n |= 16`

### Clearing Bits
To clear a bit 3 of integer `n` (i.e., set value of bit 3 to 0)
`n = n & ~(1 << 3)` (since bit 3 is odd, so 0000 0011 becomes ...)
or
`n = n & ~8` (since 8 = 0000 0100 => ~8 = 1111 1011 => n & ~8 = 0000 0000)

## Exercise
### Ex 1
How can you test whether bit `i` is set in integer `n`?
### Ex 2
What is the value of `n` `int n = 10 & -8;`

## Low-Level I/O
When we want to do low-level I/O, to bypass the buffering and abstractions provided by the C standard library, we must use system *calls* namely:
- `open()`
- `close()`
- `read()`
- `write()`

Many different flags, errors, corner cases, etc. to consider

Proper usage of low-level I/O often requires looping handling error conditions.
```C
ssize_t ret;
while (len != 0 && (ret = read(fd, buf, len)) != 0) {
	if (ret == -1) {
		if (errno == EINTR)
			continue;
	perror("read");
	break;
	}
	len -= ret;
	buf += ret;
}
```

## Low-level I/O and File Descriptors
Low-level I/O is done using *file descriptors*, which are integer values that serve as indices for open files
Each process has its own *file descriptor* table
- File descriptors `N` in process A can refer to a different file than file descriptor `N` in process B

### File Descriptors vs. File Descriptions
![[Pasted image 20260223015440.png]]

### Exec and File Descriptors
When a program calls `exec`, the new program retains the file descriptors of the original process. But the `FILE *` variables are gone, upon replacing the process image w/ the new program.

So the new program must either:
1. Perform low-level I/O using the `read()` and `write()` system calls; or
2. Use `fdopens()` to associate a new buffered file stream w/ an existing open file descriptor.

### dup and dup2
`int dup(int oldfd)`
`int dup2(int oldfd, int newfd)`
- `dup` returns a new FD that refers to the same file as `oldfd`
- `dup2` does the same, but lets you specify the value of new FD
	- `dup2` first closes `newfd` if already in use

#### Output redirection w/ dup2
```C
int main (void)
{
	int fd = open("lsout", O_WRONLY | O_CREAT, 0600);
	if (fd == -1) {
		perror("open");
		exit(1);
	}
	dup2(fd, STDOUT_FILENO) ;
	execl("/bin/ls", "ls", "-l", (char *)NULL);
	perror("execl");
	return 1;
}
```

## Pipes
A pipe is a one-way, first-in first-out (FIFO) communication channel that can be used between two processes
A pipe has two file descriptors: One for reading and one for writing 
For usage check `man 2 pipe` and for background info check `man 7 pipe`

### Using a Pipe in a Single Process
```C
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#define MSG_SIZE 13
char *msg = "hello, world\n";
int main(void)
{
	char buf[MSG_SIZE];
	int p[2];
	if (pipe(p) == -1) {
		perror("pipe");
		exit(1);
	}
	write(p[1], msg, MSG_SIZE); // No error checking: Bad
	read(p[0], buf, MSG_SIZE); // No error checking: Bad
	write(STDOUT_FILENO, buf, MSG_SIZE);
	return 0;
}
```

### Pipe for Inter-Process Communication
- Recall: Upon calling `fork()`, child process gets a copy of the parent process' file descriptor table
- So if a process creates a pipe, and then forks, the child process will have a copy of the file descriptor to the pipe
- This allows the parent and child to communicate across the pipe

#### Child Communicating to parent Over a Pipe
```C
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#define MSG_SIZE 13
char *msg = "hello, world\n";
int main(void)
{
	char buf[MSG_SIZE];
	int p[2];
	if (pipe(p) == -1) { perror("pipe"); exit(1); }
	
	if (fork() == 0) //Child writes
		write(p[1], msg, MSG_SIZE); // No error checking: Bad
	else { //Parent reads from pipe and prints
		read(p[0], buf, MSG_SIZE); // No error checking: Bad
		write(STDOUT_FILENO, buf, MSG_SIZE); // Bad
	}
	return 0;
```

### Closing Unneeded File Descriptors
- Each process should close any file descriptors that it is not using, especially w/ pipes
- If a process calls `read()` on a pipe, but there is no data ready to be read, it will **stall forever**, unless:
	- New data arrives on the pipe; or
	- **All** file descriptors (across all processes) referring to the write end of the pipe have been closed
- Again: covered in `man 7 pipe`

### More on Pipes
1. Pipes have no message boundaries. If you `write` to a pipe twice and then `read` from it, don't assume you will just receive the first chunk of data you wrote
2. `write` is guaranteed to be `atomic` only if the data is being written is smaller than a certain number of bytes (on Linux, 4KB)
3. Default pipe size is 64 KB on Linux
4. If a `write` would overflow a pipe, the process blocks (or fails if `O_NONBLOCK` flag is used when creating the pipe). Until another process empties some of the data from the pipe calling `read`