# UNIX vs. Linux vs. UNIX-like
- NIX is a proprietary OS developed by AT&T in 1969
- Free and commercial imitations followed, such as BSD, Linux, Solaris
- The macOS kernel is a BSD derivative
- We say UNIX to refer to UNIX-like OSs, often colloquially called `*nix

Linux is the most widely-used UNIX-like OS: It runs on almost any device.

![[Pasted image 20260108203930.png]]

### Common UNIX Tools/Commands
![[Pasted image 20260108204158.png]]

# Executing Programs in the Shell
```UNIX
$ gcc -o hello hello.c 
$ ./hello
```
The first line complies the file titled `hello` and the second line tells the OS to load the hello program into memory and jump to its *entry point*.
Note that the second line has `./` but the first line doesn't, because in the first line theres no need to traverse into the current directory. 

# Files in UNIX
Everything is a file is a key principle of UNIX

UNIX provides a file interface for all Input/Output
- Regular files
- Directories
- Specialfiles (ex. /dev/null, /dev/urandom)
- Physical devices
- Pipes for inter-process communication
- Network sockets

## Output Redirection
### Standard I/O Streams
- `stdin`: By default, reads input from keyboard
- `stdout`: By default, writes to the console display
- `stderr`: By default, writes to the console display

- The process treats these streams as files (surprise!)
- Use > to redirect `stdout` and 2> to redirect `stderr`
	- > overwrites the output file, >> appends
	- ex. try ls > myfiles.txt
## Pipes and Process Substitution
- Pipes transfer outputs from one process to another. (ex. ls | grep "pdf")
- Input redirection transfers the contents of a file into `stdin` of a process (ex. wc < essay.txt)
- Process substitution creates a temp file to transfer output from one or more processes to `stdin` of another process
	- ex. wc < (ls) or wc < (ls | grep "pdf")
## Interpreting Directory Listings
```UNIX
$ ls -la
total 16
drwxr-xr-x 4 bob staff 4096 Jan 6 20:18 .
drwxr-xr-x 3 bob staff 4096 Jan 6 20:18 ..
-rw-r--r-- 1 bob staff 0 Jan 6 20:16 file1
-rw-r--r-- 1 bob staff 0 Jan 6 20:17 file2
lrwxrwxrwx 1 bob staff 5 Jan 6 20:17 file3 -> file2
-rw-r--r-- 1 bob staff 0 Jan 6 20:18 .hidden
drwxr-xr-x 2 bob staff 4096 Jan 6 20:16 test1
drwxr-xr-x 2 bob staff 8192 Jan 6 20:16 test2
$
```
▶ From left to right: file permissions, link count, owning user, owning group, file size, last modified date, and file name
(symbolic link indicated by ->) (*Shortcut*)

▶ ls -ali shows inode numbers in the first column
### UNIX File Permissions

![[Pasted image 20260108210409.png]]
▶ Each file has a permission string, e.g., rw-r-xr-x
▶ rwx flags represent read, write, & execute permissions
▶ Separate permissions are assigned to three categories of users:
▶ The file’s owning user
▶ The file’s owning group
▶ All other users

▶ First column: d (directory), l (link), or - (regular file)
▶ For directories: r allows listing its contents (ls), w allows
creating/deleting directory entries, x allows entering the
directory (cd)

### Symbolic Links
Symbolic links are files that contain a reference to another file name. Like shortcuts in windows. 

### Hidden Files
```UNIX
$ ls
file1 file2 file3 test1 test2
$ ls -a
. .. file1 file2 file3 .hidden test1 test2
```

Files prefixed by a . are hidden files

## Changing File Permissions
The file owner (or root user) can change a file's permissions with `chmod`. 
- ex. `chmod o+r file.txt` grants all other users permission to read `file.txt`

Octal notation: For each user category, add up the values for r (4), w (2), and x (1)
- e.g., chmod 754 file.txt grants:
-  rwx to the owning user
-  rx to the owning group
-  r to all other users

## Globbing
**Globbing** patterns are strings that expand to match multiple file names
- Similar, but simpler than regex

`?` matches any single character
`*` matches any string, including the empty string
`[list of characters]` matches a single character inside the list, ex. `[abc]`
- Usage examples:
	- `rm *.log`: Removes all files ending in .log
	- `ls *.pdf`: Lists all files ending in .pdf
	- `*.out` references all files ending with `.out` (similar to what I added in the lab :D)