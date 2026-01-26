# PCRS
## Compiling
### The Compiler Toolchain
The `gcc` command isn't just one program; it’s a "wrapper" for a suite of tools that transform human-readable code into machine-executable binary.
#### The Four Stages of Compilation

The process moves from high-level logic to low-level hardware instructions:

|**Stage**|**Tool**|**Output**|**What Happens?**|
|---|---|---|---|
|**Preprocessing**|`cpp`|`.c` (expanded)|Handles `#include`, `#define`, and macros.|
|**Compilation**|`cc1`|`.s` (Assembly)|Translates C to assembly. Includes "Front End" (IR), "Middle End" (Optimization), and "Back End" (Target Assembly).|
|**Assembly**|`as`|`.o` (Object)|Translates assembly into machine code (binary).|
|**Linking**|`ld`|`a.out` (Executable)|Combines `.o` files and libraries (like `printf`) into one file.|

---
### Separate Compilation
For large projects, we don't compile every file at once. We compile files **separately** into object files (`.o`) and link them at the end.
#### Modularity & Risk
- **The Pro:** Speed. If you change one file in a 1,000-file project, you only re-compile that one file and re-link.
    
- **The Con:** Type Safety. Since files are compiled in isolation, the compiler can't "see" if a function's usage in `main.c` matches its definition in `sorts.c`. This leads to nasty runtime segmentation faults.
---
### Header Files & Symbols

Header files (`.h`) act as a contract or **Interface**. They ensure all files agree on the "design" before the linker tries to sew them together.
#### Managing Symbols
- **`extern`**: Declares a variable’s name and type in the header without allocating memory. Use this so multiple files can "see" a global variable defined in one `.c` file.
    
- **`static`**: Limits a variable or function to its own file. It hides the symbol from the linker, preventing "duplicate symbol" errors.
    
- **Header Guards**: Use `#ifndef` / `#define` / `#endif` blocks. This prevents a header from being included twice in the same file, which causes redefinition errors.
---
### Automating with Make
Tracking which `.c` files depend on which `.h` files is a headache. `make` automates this by looking at file timestamps.
#### Rules and Dependencies
A Makefile rule follows this syntax (the tab is mandatory):
Makefile
```C
target: dependencies
	recipe
```
### Key Features
- **Variables**: Use `OBJFILES = main.o sorts.o` to avoid repetition.
- **Pattern Rules**: Use `%.o: %.c` to tell `make` that every `.o` file depends on a `.c` file of the same name.
- **Automatic Variables**: `$@` represents the target, and `$<` represents the first dependency.
- **`.PHONY`**: Marks targets like `clean` that aren't actual files to ensure the recipe always runs.
---
## Streams
### The Concept of Streams
In C, input and output are handled via **streams**—abstract channels that act as either a source of data (input) or a destination for data (output). Instead of communicating directly with hardware, your program interacts with these logical streams.
#### Beyond Keyboard and Screen
While we often associate input with the keyboard and output with the screen, streams are versatile. A stream can represent:
- Physical devices (keyboard, monitor).
- Files on a disk.
- Network connections.
- Other programs.
---
### The Three Standard Streams
Every C program automatically opens three streams upon execution. You don't need to manually initialize them to start communicating with the user.
#### 1. Standard Input (`stdin`)
Defaults to the **keyboard**. Functions like `scanf` read from here.
#### 2. Standard Output (`stdout`)
Defaults to the **screen**. Functions like `printf` send data here.
#### 3. Standard Error (`stderr`)
Also defaults to the **screen**.

**Why have two output streams?** Separating `stdout` from `stderr` allows you to distinguish between "normal" program data and error messages. For example, you can save valid data to a file while still seeing error alerts on your monitor. While `printf` is hardcoded to `stdout`, the function `fprintf` allows you to specify which stream to use (e.g., `fprintf(stderr, "Error!");`).

---
### OS-Level Redirection
Redirection is a feature of the **Operating System (Shell)**, not the C language itself. It allows you to swap the default keyboard/screen for files without changing a single line of your code.
#### Input Redirection (`<`)
The `<` symbol tells the OS to feed a file into `stdin`.
- **Example:** `./readint < number.txt`
- The program "thinks" the file content is being typed at the keyboard.
#### Output Redirection (`>`)
The `>` symbol sends `stdout` to a file.
- **Example:** `./outputprog > results.txt`
- **Warning:** This will **overwrite** the file if it already exists.
#### Limitations
Redirection is powerful for basic tasks, but it's limited: you can only redirect to/from **one file** at a time. To read from multiple files or handle complex data logging, you must use C’s internal file-handling functions (like `fopen`).
## Files
### File Handling in C
Variables in memory are volatile; they disappear when a program ends. **Files** allow data to persist between executions. In C, we interact with files through a `FILE` pointer.
#### Opening and Closing Files
To use a file, you must first open a **stream** using `fopen` and close it with `fclose` when finished.
##### The `fopen` Function
`fopen` takes the filename and a **mode string**:
- **`"r"` (Read):** Opens an existing file. Fails if the file doesn't exist.
- **`"w"` (Write):** Creates a new file or **truncates (empties)** an existing one.
- **`"a"` (Append):** Adds data to the end of an existing file.
##### Error Handling and Closing
Always check if the `FILE *` is `NULL` before proceeding. This indicates the file couldn't be opened (e.g., missing permissions or file not found).
```C
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) {
    fprintf(stderr, "Error opening file\n");
    exit(1);
}
// ... use file ...
fclose(fp); // Returns 0 on success
```
---
### Reading Data from Streams
C provides different functions depending on whether you need raw lines or formatted data. As you read, an internal **file cursor** tracks your position.
### `fgets`: Line-Based Reading
Reads an entire line (up to $n-1$ characters) and stores it as a string.
- **Safety:** It prevents buffer overflows by requiring a maximum size.
- **Behaviour:** Stops at a newline or EOF (End of File).
### `fscanf`: Formatted Reading
Works like `scanf` but takes a file pointer as the first argument.
- **Usage:** Best for structured data (e.g., "Name Score").
- **Returns:** The number of items successfully matched.
- **Constraint:** By default, `%s` stops at the first whitespace.
- 
> **Note:** `fgets` is generally safer than `fscanf` for strings because it handles spaces and prevents overflows more reliably.
---
### Writing and Buffering
Writing to a file uses `fprintf`, which mirrors `printf` but targets a specific stream.
#### The Problem with Buffering
When you call `fprintf`, the OS doesn't always write to the disk immediately. Instead, it stores data in a **memory buffer** and writes it "in bulk" later for efficiency.
- **Risk:** If a program crashes, data in the buffer might never reach the disk.
- **Debugging Tip:** Do not rely on `printf` for debugging crashes; use `gdb`. The output you see might be "behind" where the crash actually happened.
- **`fflush`:** You can manually force the OS to empty the buffer to the disk using `fflush(fp)`
---
### Processing Files (Read-Write Workflow)
Most real-world applications follow a "Read-Process-Write" pattern, often requiring multiple file pointers simultaneously.
#### Implementation Steps:
1. **Open Input:** Open the source file in `"r"` mode.
2. **Open Output:** Open the destination file in `"w"` mode.
3. **Loop:** Read from the input (using `fgets` or `fscanf`), modify the data, and write to the output (using `fprintf`).
4. **Close All:** Close both file pointers to ensure all buffers are flushed and resources are freed.
```C
// Example: Copying names from one file to another
while (fscanf(in_fp, "%s %d", name, &score) == 2) {
    fprintf(out_fp, "%s\n", name);
}
```
## Structs
### Introduction to Structs
Arrays are useful for collections of the same type, but **structs** (structures) allow us to group related data of **different** types into a single unit. This is ideal for complex entities like a "Student," which requires a mix of strings, integers, and floats.
#### Arrays vs. Structs: Key Differences

|**Feature**|**Arrays**|**Structs**|
|---|---|---|
|**Data Types**|Homogeneous (all same)|Heterogeneous (can be different)|
|**Terminology**|Elements|Members|
|**Access Method**|Numeric Index (`arr[0]`)|Dot Notation (`s.gpa`)|
|**Size Declaration**|Subscript notation|List of member types/names|

#### Declaration and Initialization
To create a struct, you define a **structure tag** and its members. Note that the `struct` keyword is required both in the declaration and when defining variables.
```C
struct student {
    char first_name[20];
    int year;
    float gpa;
};

// Variable definition
struct student good_student;

// Accessing and assigning values
good_student.year = 2;
strcpy(good_student.first_name, "Jo");
```
---
### Structs and Functions
A critical difference between arrays and structs is how they are passed to functions. While arrays decay into pointers (modifying the original), structs are **passed by value**.
#### Pass-by-Value Behaviour
When you pass a struct to a function, C creates a **complete copy** of that struct.
- **Isolation:** Changes made to a struct member inside a function do not affect the original variable.
- **Array Copying:** Even if a struct contains an array, the _entire array_ is copied into the function's scope.
- **Inefficiency:** Copying large structs is slow and memory-intensive. Returning a modified struct also requires a second copy, making it a "wasteful" solution for large data sets.
---
### Pointers to Structs
To avoid the overhead of copying and to allow functions to modify original data, we use **pointers to structs**. This allows a function to operate directly on the memory address of the original struct.
#### The Arrow Operator (`->`)
Accessing struct members via pointers requires dereferencing. However, the dot operator (`.`) has higher precedence than the dereference operator (`*`).
- **The Unwieldy Way:** `(*p).gpa` (Parentheses are mandatory).
- **The Elegant Way:** `p->gpa`.
#### Syntactic Review
The arrow operator is shorthand for "dereference the pointer, then access the member."
```C
struct student s = {"Jo", "Smith", 2, 3.2};
struct student *p = &s;

// These two lines are identical:
(*p).gpa = 3.8; 
p->gpa = 3.8; 
```
All standard pointer rules apply: you must initialize a struct pointer to a valid address (using `&`) before dereferencing it, or you will trigger a segmentation fault.
## Low-Level I/O
### PCRS Binary Files and Structs
Unlike text files, which interpret bytes as characters, **binary files** store data in the same raw format used by the computer's memory. This makes them smaller and faster for machines to read, though they look like "junk" in a standard text editor.

---
#### 1. Binary vs. Text Handling
Text files rely on specific character encodings (like ASCII) and line breaks. Binary files treat data as a continuous stream of bytes.
##### Opening Binary Streams
To interact with binary data, you must append `b` to the mode string in `fopen`.
- **`rb`**: Read binary.
- **`wb`**: Write binary (truncates existing).
- **`ab`**: Append binary.
---
#### 2. Binary Input and Output
Binary I/O requires explicit knowledge of the data's **size** and **quantity**.
##### The `fwrite` and `fread` Functions
These functions are "duals" of each other, using nearly identical parameters.

|**Parameter**|**Purpose**|
|---|---|
|**`void *ptr`**|Pointer to the data buffer (source for write, destination for read).|
|**`size_t size`**|Size of a **single** element (e.g., `sizeof(int)`).|
|**`size_t count`**|Number of elements to process.|
|**`FILE *stream`**|The open file pointer.|

> **Warning:** Both functions return the **number of elements** successfully processed, not the number of bytes. If you ask for 5 ints and get 3, an error or EOF occurred.
##### Endianness and Portability
Binary files are often **not portable** between different types of computers.
- **Big-Endian**: Stores the most significant byte at the smallest address.
- **Little-Endian**: Stores the least significant byte at the smallest address.
    
    If you write an integer on a Little-Endian machine and read it on a Big-Endian one, the value will be incorrect.
---
#### 3. Application: WAV Files and Structs
Binary files allow us to store complex data structures directly.
##### WAV Audio Structure
A standard WAV file consists of:
1. **Header (44 bytes)**: Metadata like sample rate and file size.
2. **Samples**: 2-byte "short" integers representing audio amplitude.

By skipping the first 44 bytes using `fseek` or a dummy `fread`, you can manipulate the raw audio (e.g., multiplying each sample by 4 to increase volume).
##### Writing Structs to Disk
You can write an entire `struct` in one `fwrite` call.
- **Padding and Alignment**: Compilers often add "invisible" bytes between struct members to align them with memory boundaries.
- **The `sizeof` Rule**: Never manually calculate struct size. Always use `sizeof(struct student)` to ensure the padding is included correctly.
---
#### 4. Random Access in Files
You don't always have to read from start to finish. C allows you to "jump" to specific byte offsets.
### Using `fseek`
`fseek` moves the file's internal position cursor.

$$fseek(stream, offset, whence)$$

|**Whence Constant**|**Reference Point**|
|---|---|
|**`SEEK_SET`**|Offset is relative to the **beginning** of the file.|
|**`SEEK_CUR`**|Offset is relative to the **current** cursor position.|
|**`SEEK_END`**|Offset is relative to the **end** of the file (use negative values).|

##### The `rewind` Function
A specialized, simpler version of `fseek`. Calling `rewind(fp)` is identical to `fseek(fp, 0, SEEK_SET)`. It resets the cursor to the very beginning of the file, allowing you to re-read or re-write data.


# PCRS String Summary
In C, a string is an array of `chars` terminated by `\0` (`NULL` byte)
C standard library offers string manipulation functions, defined in `string.h`

# String Manipulation and Memory Safety
String manipulation is a major cause of memory errors (ex. `buffer` *overflow*)
The C standard library has *safe* and *unsafe* string functions
	**Some** unsafe functions can be sued safely if the string is **guranteed** to be NULL-terminated: `strlen(argv[o]);`
	But even so-called "safe" functions can cause memory errors if used improperly: `char x[2];` `strncpy(x, "blabla", 7);`

## Unsafe String Functions: Example
From `man` gets (Linux):
	Never use `gets()`. Because it is impossible to tell without knowing the data in advance how many characters `gets()` will read, and because `gets()` will continue to store characters past the end of the buffer, it is dangerous to use. Breaks computer security. use `fgets()` instead.
	The `gets()` function is aslo flawed in its lack of bounds checking, and inability for the calling program to reliably determine the length of the next incoming line. The use of it allowed evil users to do a buffer overflow attack, so its strongly advised to use `fgets()`.

Because of C's extensive toolset and required skill level for such toolset, it is advised to only use C when necessary. Otherwise be responsible and careful. 
![[Pasted image 20260125194215.png]]

How can we safely copy a string?
```C
// Option 1:
strcpy(dest, src);

// Option 2:
#define MAXS 100
char dest[100];
strncpy(dest, src, MAXS);

// Option 3:
#define MAXS 100
char dest[100];
strncpy(dest, src, MAXS - 1);

// Option 4:
#define MAXS 100
char dest[100]; dest[0] = '\0';
strncat(dest, src, MAXS - 1);
```
The `MAXS` just ensures that we don't cover over the 100 cap.

### Testing Safe Skills
Is this a safe code snippet?
```C
char str1[20] = "BeginnersBook";
printf("Length of string str1 %d\n", strnlen(str1, 30));
printf("Length of string str1 %d\n", strnlen(str1, 10));
```
I say no! Because the size of the String is inherently 20, but we attempt to access 30.
Answer: **Safe** — but could produce incorrect result due to `30` access in size `20` array

```C
char str1[6] = "csc209";
// some other code here.
int b;
scanf("%d", &b);
printf("Length of string str1 %d\n", strnlen(str1, b));
```
I say no, because we are trying to get the string length up until index `b`, but  index `b` (may be) undefined and `NULL`.
Answer: **Unsafe**, Insufficient space is allocated as it trusts users to give a max index. 

```C
char buf[209] = {'\0'};
printf("Enter your name and press <Enter>\n");
gets(buf);
```
I say no, because we just learned how the `gets()` function can be exploited, thus its unsafe to use and easily exploited for a buffer overflow. 
Answer: **Unsafe**, you should use `fgets()` since `gets()` accepts any length stream (leads to potential for overload)

Then we conclude lec with a demo of `gdb`.



