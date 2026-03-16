# PCRS

## Signals

In Linux and C, signals are a mechanism used by the operating system or other processes to interrupt a currently running process and notify it that a specific event has occurred.
### 1. Common Signals and Default Actions

Each signal is identified by a number (typically between 1 and 31) and a defined constant. By default, processes perform a specific action when receiving a signal.

|Action|Signal Constant|Trigger Example|Default Behavior|
|---|---|---|---|
|**Interrupt**|`SIGINT`|`Ctrl+C`|Terminates the process.|
|**Suspend**|`SIGSTOP`|`Ctrl+Z`|Suspends execution (can be resumed).|
|**Continue**|`SIGCONT`|`fg` command|Wakes up a suspended process.|
|**Segmentation Fault**|`SIGSEGV`|Dereferencing a `NULL` pointer|Terminates the process.|

### 2. Sending Signals

You can send arbitrary signals to a process either from the command line or directly from within a C program. In both cases, you need to know the target's Process ID (PID), which you can find using the `ps` command in the terminal or via the return value of `fork()` in C.

- **Command Line:** Use the `kill` program (e.g., `kill -STOP <pid>` or `kill -INT <pid>`).
    
- **C Library Function:** Use the `kill(pid, signal)` function to send signals programmatically (e.g., to send a message from a parent to a child).

### 3. Signal Handling and the PCB

When a signal is delivered, how does the process know what to do?

- The **Process Control Block (PCB)** contains a **signal table** (similar to an open file table).
    
- Each entry in this table holds a pointer to the code (the **signal handling function**) that should execute when that specific signal arrives.

### 4. Customizing Signal Behavior with `sigaction`

You can change a signal's default behavior (e.g., to print a message instead of terminating) by installing a custom signal handler using the `sigaction` system call.

**Steps to create a custom handler:**

1. **Write the Handler:** Create a function that takes a single `int` parameter and returns `void`.
    - _Crucial Note:_ If you do not explicitly call `exit()` inside your custom handler, control will return to the exact point in the process where it was originally interrupted once the handler finishes.
        
2. **Configure `struct sigaction`:** Create this struct and set its `sa_handler` member to point to your new function. (You also set `sa_mask` to empty to ensure no signals are blocked during the handler's execution).
    
3. **Install it:** Call the system call `sigaction(signal_number, &new_action, &old_action)` to update the PCB's signal table.

### 5. Uncatchable Signals
You cannot create an "unkillable" process by overwriting all termination signals. The operating system restricts two specific signals from being changed or ignored:

- **`SIGKILL`**: Will _always_ force the process to terminate.
    
- **`SIGSTOP`**: Will _always_ force the process to suspend.




# Lecture
- Signals are unexpected, asynchronous events that can happen at any time.
- Unless you make special arrangements, signal terminate your process

## Signals We Already Use
- Signals are another form of Inter-Process Communication
- We've already used well established signals through the shell via `ctrl+c` and `ctrl+z` key combinations

When you hit `ctrl+c` the process terminates, specifically, it sends `SIGINT` (signal interrupt). This is a *gentle* termination as opposed to the `kill` command

Similarly using `ctrl+z1` triggers a `SIGTSTP` signal ("stop typed at terminal").

## Various Signals
`SIGSEGV` — Invalid memory reference
`SIGFPE` — Floating-point exception
`SIGILL` — Illegal instruction

There are also user-defined signal `SIGUSR1` and `SIGUSR2` that we can use for our own purposes. By default they terminate the process unless we change them.

## Signals via Shell
To send a signal `SIGNAME` to one or more processes:
`$ kill -SIGNAME pid`

For example:
`$ kill -SIGINT 11248`
`$ kill -SIGKILL 11248`

The `SIGKILL` and `SIGSTOP` signals cannot be caught, blocked, or ignored.

## Signal Handling
There are 3 options for handling signals:
1. Write a signal handler function which will be called w/ receipt of a signal
2. Ignore the signal (i.e., the signal does nothing the process)
3. Use the default action

### Changing the Default Action
Two options for changing:
1. The `sigaction()` system call
2. The `signal()` C standard library function

`signal()` is cross-platform but more limited in scope, whereas `sigaction()` is more flexible but found only on POSIX.1-compliant systems

#### Protip!
`sigaction` is both the name of the system call and the struct that takes as the 2nd and 3rd arguments
```C
int sigaction(int sig, const struct sigaction *act, struct sigaction *oldact);
```

## What is a Signal Action?
```C
struct sigaction {
	void (*sa_handler)(int);
	void (*sa_sigaction)(int, siginfo_t *, void *);
	sigset_t sa_mask;
	int sa_flags;
	void (*sa_restorer)(void);
}
```

`sa_handler` can be set `SIG_IGN` (ignore), `SIG_DFL` (default action), or the address of a handler function.

```C
/* EXAMPLE */
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
int i = 0;
void handler(int signo) {
	fprintf(stderr, "Sig %d; total %d.\n", signo, ++i);
}
int main(void) {
	struct sigaction newact;
	sigemptyset(&newact.sa_mask);
	newact.sa_flags = 0;
	newact.sa_handler = handler;
	if (sigaction(SIGINT, &newact, NULL) == -1) exit(1);
	if (sigaction(SIGTSTP, &newact, NULL) == -1) exit(1);
	
	for(;;); //Infinite loop
}

```

### Sending A Signal From One Process to Another
One process can send a signal to another process using the misleadingly-named `kill()` system call

## Limitations of Signals:
### Information
- Signals conveys no information, aside from what type (`SIGINT`, `SIGUSR1`, etc.) it is
- Generally only used to indicate abnormal conditions: Not for data exchange
### Queuing
- Multiple instances of the same signal do not queue
- If signal `X` is sent while a prev-sent signal `X` is pending, then the second `X` is lost (TLDR: Can only run one signal at a time, overrides prev. signal)
- Ex. if your process a `SIGCHLD` (Child stopped or terminated), it may be that only one child process has terminated, or that *multiple* child processes have terminated

## Signal Sets
A signal set (`sigset_t`) is a *bit vector* that specifies the set of signals to block; operate on them using the following standard library function:
```C
int sigemptyset(sigset_t *set);
int sigfillset(sigset_t *set);
int sigaddset(sigset_t *set, int signo);
int sigdelset(sigset_t *set, int signo);
int sigismember(const sigset_t *set, int signo);
```

Note: Recall last week about bitwise operators

### Masking Signals During Program Execution
- As our program may receive signals randomly at any time
- This *temporary* block is different from ignoring a signal entirely
- Use the `sigprocmask()` system call to examine or change the set of blocked signals, via a *mask* (i.e., a bit vector representing a set of signals)

#### Masking Signals: Examples
```C
sigset_t set, oldest;
sigemptyset(&set);
sigaddset(&set, SIGINT);
sigpromask(SIG_BLOCK, &set, &oldset);
/*... Critical operation ...*/
sigpromask(SIG_SETMASK, &oldset, NULL);
```

#### Masking Signals During Signal Handler Execution
sa_mask specifies a mask of signals which should be blocked (i.e., added to the signal mask of the thread in which the signal handler is invoked) during execution of the signal handler. In addition, the signal which triggered the handler will be blocked, unless the SA_NODEFER flag is used. 

#### Async Signal Safety
Suppose a program is in the middle 


# MAKE SURE TO KNOW THIS FOR EXAM 