# Lecture
- Make files are useful - allowing us to revisit bash scripting
- The project doesn't depend on the makefile, so the content is convenient to cover now.

## PCRS Recap:

**Compiling a C Program**
- C programs consist of multiple `.c` files
- Each individual `.c` file can be compiled to an *object* file
- Object files contain "placeholders" for addresses of functions that were *declared* but not *defined*
	- Header (`.h`) files ensure consistency between function declarations across your program's multiple source files
- The *linker* connects object files together to create an *executable* file

**Makefiles**
- Make files facilitate *building* (i.e., compiling, linking, and sometimes even testing and packaging) projects consisting of multiple sources of files
- If only one source file ha been changed, no need to recompile everything; instead:
	1. Recompile source files that have changed
	2. Relink updated object files to generate a new executable file

A makefile contains a sequence of *rules*, each in the format:
```C
target: prereq_1 prereq_2 ... prereq_n
	action_1
	...
	action_n
```

**Using make**
- Makefiles are processed by the `make` program
- Run `make` w/ no arguments to evaluate first rule
- Run `make` `TARGET` to execute action(s) defined in rule for `TARGET`
	- Only if `TARGET` prerequisites were modified since last time that `make` `TARGET` was run
- To force make `TARGET` to recompile code, you can:
	- Update last modified time of prerequisite source files, w/ `touch` or,
	- Delete prerequisite object files

## Makefile Syntax:
### Defining Variables
You make define variables; e.g., to store compiler flags:
```C
CFLAGS= -g -Wall -Werror -fsanitize=address
reverse : reverse.c
	gcc $(CFLAGS) -o reverse reverse.c
```

### Automatic (Built-In) Variables
![[Pasted image 20260325111420.png]]
```C
CFLAGS= -g -Wall -Werror -fsanitize=address
hello: hello.c hello.h
	gcc $(CFLAGS) -o $@ $<
```

## Makefile Example:
```C
FLAGS= -Wall -Werror -fsanitize=address -g
OBJ = simfs.o initfs.o printfs.o simfs_ops.o
DEPENDENCIES = simfs.h simfstypes.h

all : simfs

simfs : ${OBJ}
	gcc ${FLAGS} -o $@ $ˆ
%.o : %.c ${DEPENDENCIES}
	gcc ${FLAGS} -c $<
clean :
	rm -f *.o simfs
```

### Pattern Rules
```C
%.o : %.c ${DEPENDENCIES}
	gcc ${FLAGS} -c $<
```
- Most files are compiled in the same way, so we write a pattern rule for a general case
- `%` expands to the stem of the file name (i.e., w/o extension)
- `gcc -c` compiles the source file(s), but does not link

### Phony Targets
You may want a command that builds a target:
```C
OBJ = simfs.o initfs.o printfs.o simfs_ops.o
simfs: ${OBJ}
	gcc ${FLAGS} -o $@ $ˆ
```
Or a target that doesn’t build anything:
```C
clean:
	rm -f *.o simfs
```

## C Programming vs. Systems Programming
- Now we understand C and its key features (though still some not yet worked w/ like enums and unions)
- Many concepts covered thus far like pointer to pointer arithmetic are universal to systems-level programming

### Pointers and the Memory Model
#### Use After Return
```C
int *foo() {
	int a = 10;
	return &a;
}
int main() {
	int *b = foo();
	printf("%d", *b);
	return 0;
}
```

#### Use After Scope
```C
int main() {
	int *b;
	for (int i = 0; i < 10; i++) {
		b = &i;
	}
	printf("%d", *b);
	return 0;
}
```

### Key Takeaways
- Understand when memory is allocated and deallocated in your program
	- Non-static variables are *automatically* allocated/freed upon entering/exiting scope
	- Dynamically-allocated memory is your responsibility to allocate/free
- Familiarize yourself w/ the complete list of errors that Address Sanitizer detects
- Understand how C syntax translates to what the system actually does under the hood

## Systems Programming
- The *systems-level* aspect of the course, benefits from awareness of the hardware and operating system being used
	- Unlike Java or Python, which act as a "translation layer" that make all platforms appear similar
- When reviewing labs for the exam, think about what we've learned about the underlying system

### System Calls vs. Library Calls
- System calls are the interface by which programs request services from the operating system kernel
- Standard C library functions (e.g., string library functions) are **not** system calls
	- Some serve as "wrappers" around system calls (like how `fopen()` calls `open()`)

### System Calls and Portability
- Usage of C standard library functions is *portable*
	- But need to recompile on different platforms
- Usage of system calls is **not** portable, unless you...
	- Use (e.g., POSIX-compliant) system calls supported by multiple operating systems (see `CONFORMING TO` heading in system call man pages)
	- Write separate implementations of OS-dependent code, compile multiple platform-specific executable (using C preprocessor macros)

### Error Checking
- All system calls are some library functions use `errno` to return error values
	- See `man` `errno` for completer list of error names defined in `errno.h`

Simplistic use of system calls is not suitable for proper error handling. Example below demonstrates proper usage of `read()` to read `len` bytes.
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