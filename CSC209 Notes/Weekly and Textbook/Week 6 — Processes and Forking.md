# PCRS
## System Calls
### System Calls vs. Library Functions in C
In C programming, the distinction between a library function and a system call is often hidden behind similar syntax, but their execution paths are fundamentally different.
#### Coding Examples
**1. Output: `printf` vs. `write`**
- **Library Function:** `printf("Age: %d", 25);`
    This formats the integer into a string. It is a user-level library function.
- **System Call:** `write(1, "Age: 25", 7);`
    `printf` eventually calls `write`. The OS takes the raw bytes and sends them to the hardware (standard output).
    

**2. Input: `scanf` vs. `read`**
- **Library Function:** `scanf("%f", &price);`
    This parses a string into a float.
    
- **System Call:** `read(0, buffer, 10);`
    `scanf` uses `read` to fetch raw characters from the keyboard buffer managed by the OS.
    
**3. Files: `fopen` vs. `open`**
- **Library Function:** `FILE *fp = fopen("data.txt", "r");`
    This creates a buffered stream in user space.
- **System Call:** `int fd = open("data.txt", O_RDONLY);`
    `fopen` calls `open` to get a "file descriptor" from the kernel.
#### Comparison Table

|**Use Case**|**Library Call (High-Level)**|**Underlying System Call (Low-Level)**|
|---|---|---|
|**Terminate**|`exit(0);` (Standard cleanup)|`_exit(0);` (Immediate kernel exit)|
|**Output**|`printf("Hello");`|`write(1, "Hello", 5);`|
|**Input**|`fgets(str, 10, stdin);`|`read(0, str, 10);`|
|**Memory**|`malloc(1024);`|`brk()` or `mmap()`|

#### How It Works
When you call `write()`, the CPU triggers a **software interrupt**. Control shifts from your program to the **Operating System kernel**. The OS performs the task, then returns a status code (e.g., number of bytes written) back to your C program.

## Errors and Errno
System calls interact with resources outside your program's control, making failure a constant possibility. Proper error handling prevents "silent" failures and cryptic crashes like segmentation faults.
### Detecting Failures
System calls indicate failure through specific return values:
- **Integers:** Functions like `stat()` or `open()` return `-1`.
- **Pointers:** Functions like `malloc()` or `fopen()` return `NULL`.

Always verify these returns before proceeding. For example, using a `NULL` file pointer from a failed `fopen()` will crash your program. **ADD THIS TO MY CSC209 PROJECT**
### The `errno` Variable and `perror`
When a system call fails, the OS sets a global integer variable called `errno` to a specific error code (e.g., `ENOMEM` for out-of-memory).

- **`perror(const char *s)`**: This is the preferred way to report system errors. It prints your custom string `s`, followed by a colon and the human-readable description of the current `errno`.
    
- **Example:** `perror("fopen")` might output `fopen: No such file or directory`.
### Choosing Between `perror` and `fprintf`
Not every "unsuccessful" call is a system error that sets `errno`.

- **Use `perror`**: For actual system failures (e.g., `malloc` fails, or `stat` can't find a file).
    
- **Use `fprintf(stderr, ...)`**: For logical conditions or non-error "failures." For example, `fgets` returning `NULL` at the end of a file is normal behavior, not an OS error; `perror` would incorrectly report "Undefined error: 0."
### C Coding Examples
```C
// 1. Handling fopen failure
FILE *fp = fopen(argv[1], "r");
if (fp == NULL) {
    perror("Error opening file");
    exit(1); 
}

// 2. Handling malloc failure
int *arr = malloc(sizeof(int) * LARGE_NUMBER);
if (arr == NULL) {
    perror("malloc failed");
    exit(1);
}

// 3. Handling stat failure
struct stat sbuf;
if (stat("missing.txt", &sbuf) == -1) {
    perror("stat"); // Avoids using uninitialized sbuf data
}
```

### Practical Error Checking: Building a Robust "Head" Program
Effective error checking provides users with feedback, helps developers find bugs faster, and prevents crashes like **segmentation faults**.
#### 1. Validating Command Line Arguments

Before processing data, verify that the program was called correctly to avoid accessing null pointers in `argv`.

- **Argument Count:** Check `argc` to ensure the user provided the required number of inputs.
```C
if (argc != 3) {
	fprintf(stderr, "Usage: %s <num_lines> <filename>\n", argv[0]);
	exit(1);
}
```
- **String Conversion:** Use `strtol()` instead of `atoi()` to convert the "number of lines" argument. `strtol` allows you to check `errno` for out-of-range errors (`ERANGE`) and verify if any numeric conversion actually occurred.

#### 2. Handling System and Library Failures
Interacting with files requires checking return values immediately.
- **File Opening:** If `fopen()` returns `NULL`, use `perror()` to print the specific system error (e.g., "No such file or directory").
    
- **Reading Data:** If `fgets()` returns `NULL` before the loop finishes, it signals the end of the file (EOF). Use `fprintf()` to explain that the file was shorter than requested.

#### 3. Logic and Buffer Management
Small test cases (e.g., setting `BUFSIZE` to 10) help uncover edge-case bugs that large tests might hide.
- **Partial Reads:** If a line exceeds `BUFSIZE`, `fgets()` will only read a portion. To fix this, check if the buffer contains a newline character (`\n`). If not, keep calling `fgets()` until the full line is consumed.
#### Summary Table: Error Reporting

|**Error Type**|**Detection**|**Reporting Tool**|
|---|---|---|
|**System/OS Error**|Return `-1` or `NULL`|`perror("description")`|
|**Program Logic/EOF**|Specific return state|`fprintf(stderr, "msg")`|
|**Usage Error**|`argc` check|`fprintf(stderr, "Usage: ...")`|

Developing the habit of checking every system call makes your code slightly longer but significantly more robust and professional.
## Process Model
### 1. Programs vs. Processes
To understand how an operating system (OS) runs code, we must distinguish between these two terms:
- **Program:** The static file containing human-readable source code or compiled machine code stored on disk.
- **Process:** A dynamic, running instance of a program. It includes the machine code plus the current execution state (variables, registers, and memory).
#### Memory Layout
When a process is loaded into memory, it follows a structured layout:
- **Code/Text:** The machine instructions.
- **Global Data:** Static and global variables.
- **Heap:** Dynamically allocated memory (via `malloc`).
- **Stack:** Tracks function calls and local variables.

#### OS Tracking: The Process Control Block (PCB)
The OS manages processes using a **Process ID (PID)** and a **PCB**. The PCB stores critical state information, such as the **Program Counter** (next instruction) and **Stack Pointer** (top of the stack). You can view active processes on Linux using the `top` command.

---
### 2. Process States and Scheduling
Processes move through different states during their lifecycle:

- **Running:** Currently executing instructions on a CPU.
- **Ready:** Prepared to run but waiting for an available CPU.
- **Blocked/Sleeping:** Waiting for an event (e.g., I/O completion or a `sleep` timer).

The OS **Scheduler** switches between processes so rapidly that it creates the illusion of simultaneous execution.

---
### 3. Creating Processes with `fork()`
The `fork()` system call creates a new process by duplicating the existing one.

- **Parent:** The original process. `fork()` returns the **Child's PID**.
- **Child:** The new process. `fork()` returns **0**.
- **Execution:** Both processes are nearly identical (same variables, same code location) but have separate memory spaces. Changes in the parent do not affect the child.
---
### 4. Coordination: `wait()` and `exit()`

The parent and child run concurrently; the order of execution is non-deterministic. To synchronize them, the parent uses the `wait()` system call.

- **`wait()`**: Suspends the parent until a child terminates. It returns the child's PID and a **status** integer.
- **Status Macros:** Since the status integer encodes multiple values, we use macros like `WIFEXITED` (checked if successful) and `WEXITSTATUS` (extracted the exit code).
---
### 5. Zombies and Orphans

- **Zombie:** A process that has finished (`exit`) but whose parent hasn't called `wait`. The OS keeps its entry in the process table to store the exit status.
- **Orphan:** A child whose parent terminated first. Orphans are "adopted" by the **init** process (PID 1), which automatically waits for them to prevent them from remaining zombies.

---

### 6. Running New Programs with `exec`
While `fork` creates a copy, the `exec` family of functions **replaces** the current process's code and data with a new program.

- **Key Behavior:** The PID remains the same, but the original code is gone. `exec` only returns if it fails.
    
- **Variants:** `execl` (list of args), `execv` (vector/array of args), and `execvp` (searches the system `PATH`).

#### How a Shell Works
When you type a command:
1. The shell **forks** a child.
2. The child calls **exec** to load the desired program.
3. The parent (shell) calls **wait** until the child finishes, then prints a new prompt.

# Processes
A *process* is an instance of an executing *program*
Executing multiple instances of the same program launches multiple processes
- Ex. Running multiple instances of notepad
A single instance of a program may launch multiple processes
- Ex. One process per Google Chrome or Firefox tab

## Process Memory
Each process has its own memory space including its own stack and heap.
A process cannot access the variables/memory of another process. (Actually not true, discussed in CSC369)
## Process Creation
In UNIX-like systems, processes are created with the `fork()` system call
The process calling `fork()` is the *parent* of the newly-created *child* process.
Try `pstree` from a bash shell to see a tree of the currently-running processes
## Process Identifiers
Each Process has a PID (process identifier)
- The first process created when the system boots up is the `init` process with PID 1.
	- On Ubuntu the `init` process is `systemd`, but on macOS it is `launchd`

# Program Exit Status vs. Process Exit Status
Every program reports an *exit status* upon completion/termination. This is done via `exit()`.
`void exit(int status)`
Here, `status` is the program's *exit status*, which becomes part of the *process' exit status*

## Obtaining the Exit Status
A process exit status is saved to be reported back to the parent process via `wait` and `waitpid`. If the program exited, the status includes as its low-order 8 bits the program exit status.
![[Pasted image 20260208200754.png]]

## Exit vs. Return
Inside `main()`, `return` and `exit()` are nearly equivalent:
1. The return value of `main()` is the *program exit status* passed to `exit()`
2. `exit()` performs some cleanup (ex. flush `stdio` streams) and calls `_exit()`
3. `_exit()` sets the *process exit status*, or *termination status*, and terminates the process

Outside of `main()`, use `exit()` to terminate the process
### Exercise
Try:
```C
#include <stdio.h>
#include <stdlib.h>
int main() {
	printf("Hi");
	exit(0); // equivalent to "return 0;"
}
```
vs. 
```C
#include <stdio.h>
#include <unistd.h>
int main() {
printf("Hi");
	_exit(0);
}
```
What's the difference?
Answer: While they both terminate the process, `exit()` tidies up everything before exiting, while `_exit()` does not, just dropping everything, so useless buffers still exist.

**Return 0 on a success, and any other value for an error**

# Questions
How do we obtain the program exit status from the process exit status?
Ans: Use macros defined in `wait.h` (see man pages)
- ex. WIFEXITED(status) to see if process terminated normally or abnormally
- WEXITSTATUS(status) to obtain program exit status

What happens if a process terminates before its exit status is obtained by its parent?
Ans: The child becomes a *zombie process*.
 - The operating system retains minimal information about the process until the parent obtains the exit status via `wait()`.

What happens if a parent process terminates before waiting for all its children?
Ans: The children become *orphan processes*
- Orphan processes are *adopted* by the `init` process.

Which statements are true when fork() is called?
Q1: True/False? The child process shares the same PID as the parent process.
False - Each process has a unique PID

Q2: True/False? fork() will only fail if the total number of processes under execution by a single user would be exceeded.
False - While exceeding the users process limit can cause failure, its not the only possible reason. Ex. memory depleted, or max number of PID's reached

Q3: True/False? After fork() has been called successfully, a value of 0 is returned to the child
True

# File Descriptors
The `FILE` structure used by `stdio` functions (ex. `fopen`) includes an integer called *file descriptor*.
The OS maintains a per-process *file descriptor table*
A child gets a **copy** of the parent's *file descriptor table*
So all open files open in the parent **before** the `fork` are also open in the child.
File descriptors in different processes may refer to the same *open file description* in OS's *open file table*.
File descriptors in different processes can refer to the same *open file description* in the OS's *open file table*.
NOTE: Immediately after the `fork`, both parent and child's file descriptor refer to the same *open file descriptions* in the system-wide *open file table*.

## File Descriptors vs. File Descriptions
![[Pasted image 20260208203306.png]]

### Sharing the Same Open File Description
If the parent process opens a file before a `fork`:
- Whichever process (parent or child) calls `fread` first will read the first `N` bytes
- Whichever process calls `fread` afterwards will read the next `M` bytes

Since both parent and child processes refer to the same file description, they both share the same *offset* value.

## Inter-Process Communication
Exit statuses provide a very limited form of *inter-process communication*, specifically between a child and a parent process.

Next week, we will discuss *pipes*, which are a more useful mechanism for process communication.

Open pipes are also associated w/ file descriptors, so they can be read/written very similarly to regular files. 

# The `exec` Functions
The `exec` functions *load* a new program into the current process image. The process retains its original PID.

The functions differ mainly in how they are called, ex.
```C
char* args[] = {"ls", NULL};
execve("/bin/ls", args, NULL);
```
vs. 
```C
execle("/bin/ls", "ls", NULL, NULL);
```

Below in an image outlining the differences between the `exec` functions.
![[Pasted image 20260208203847.png]]
Also: `execve()` is a system call; the others are C standard library functions

## `exec` and the Shell

A shell can use `fork` and `exec` to execute other programs:
1. Shell process p waits for keyboard input.
2. You type `ls`.
3. Shell forks child process c.
4. Process c uses an `exec` function to run `ls`.
5. Process `p` calls wait to wait for c to terminate, and then prints new prompt.

## `exec`and File Descriptors
When a program calls `exec`, the new program still retains the file descriptors of the original process.
But the `FILE *` variables are gone, upon replacing the process image with the new program.

So the new program must either:
1. Perform low-level I/O using the read() or write() system calls (you will do this with pipes on Assignment 2).
2. Use fdopen() to associate a new buffered file stream with an existing open file descriptor.

## Security Imlications of `exec` Functions
Using execlp and execvp can be very dangerous when used improperly. Can you find out why?

The danger primarily stems from the `p` in their names, with represents the PATH. Unlike their counterparts `execl` and `execv`, that requires a full, absolute file path, these functions search through the directories listed in the `PATH` to find the executable. This allows an attacker to access your file path and greatly increases risk in what they are capable of doing (such as naming a file as the exact same thing as something in your PATH, so instead of running your file, you run theirs)
