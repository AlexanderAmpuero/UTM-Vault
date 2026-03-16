
# PCRS
## Multiplexing I/O with Select

In systems programming, we often need to monitor multiple file descriptors (pipes, sockets, etc.) at once. Standard `read()` calls can block your entire program, leading to inefficiency or deadlocks.
### 1. The Problem: Blocking I/O

When you call `read()` on a pipe or socket, the process enters a **blocked** state if no data is currently available. It stays there until:

1. Data is written to the other end.
2. The other end is closed (returning 0).

#### The "Multi-Source" Trap
Suppose a parent process has two children ($C1$ and $C2$) with two separate pipes. If the parent reads from $C1$ first, but $C1$ is busy or silent, the parent blocks. Even if $C2$ has "lots to say," that data sits in the pipe unread.

If $C2$'s pipe fills up entirely, $C2$ will also block on its `write()` call. Now, both the parent and $C2$ are stuck—a classic synchronization headache.

---
### 2. The Solution: `select()`
The `select()` system call allows a program to monitor multiple file descriptors and wait until one or more of them becomes **"ready"** for I/O.

> **Definition of "Ready":** A file descriptor is ready if a subsequent I/O operation (like `read()`) will not block. This happens when data is available or the resource has been closed.
---
### 3. File Descriptor Sets (`fd_set`)

To tell `select()` which descriptors to watch, we use the `fd_set` type. It is essentially a bit-field where each bit represents a file descriptor.

Because we don't manipulate the bits directly, C provides four macros for management:

|**Macro**|**Purpose**|
|---|---|
|**`FD_ZERO(&set)`**|Clears the set (removes all FDs).|
|**`FD_SET(fd, &set)`**|Adds a specific FD to the set.|
|**`FD_CLR(fd, &set)`**|Removes a specific FD from the set.|
|**`FD_ISSET(fd, &set)`**|Returns true if the FD is still in the set (used after `select` returns).|

---

### 4. The `select()` API

```C
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

- **`nfds`**: This must be the value of the **highest-numbered file descriptor plus one**. (e.g., if you are watching FDs 3 and 5, `nfds` should be 6). This is for kernel efficiency.
- **`readfds`**: The set of FDs to watch for data available to read.
- **`writefds` / `exceptfds`**: Sets for monitoring write-readiness or error conditions (often set to `NULL` if not needed).
- **`timeout`**: A pointer to a `struct timeval` that specifies how long to wait. If `NULL`, `select()` blocks indefinitely until an FD is ready.

---

### 5. The Workflow

Using `select()` follows a specific pattern because **`select()` modifies the sets passed to it**.

1. **Initialize**: Call `FD_ZERO()` and `FD_SET()` to build your "watch list."
2. **Call Select**: The process sleeps until activity occurs.
3. **Check Results**: When `select()` returns, the `fd_set` only contains the descriptors that are actually ready.
4. **Action**: Use `FD_ISSET()` to check each FD. If it's still in the set, it’s safe to `read()`.
5. **Repeat**: Because `select()` cleared the non-ready FDs from your set, you **must** re-initialize the set before calling `select()` again in a loop.

### 6. Comparison: Traditional Read vs. Select

|**Feature**|**Traditional read()**|**select()**|
|---|---|---|
|**Behavior**|Wait for one specific source.|Wait for any of many sources.|
|**Efficiency**|High CPU idle if the source is slow.|High; only wakes when action is needed.|
|**Complexity**|Simple logic, but prone to blocking.|More boilerplate (macros, max FD + 1).|

# Lecture
## Reading From Multiple File Descriptors
Assume that process `p0` has any 2 file descriptors open for reading (socket, file, pipe, etc.)
![[Pasted image 20260316003021.png]]

If `p0` reads from `fd0`, it will block until `fd0` has data ready to read. But what if `fd2` already has data available to read? We have multiple solutions to this:

### Solution 1: Fork
`p0` can `fork` one child process per file descriptor to read from. Each child then calls `read` on one file descriptor and communicates data through a pipe to the parent.
![[Pasted image 20260316003224.png]]
Which should be called first though, `read()` or `wait()`? I say wait().

- It's common for server softwares to use `fork()` to make a new process for each client that connects: SSH does exactly that.
- *Performance* benefit: Solves the issue of blocking `read()` calls
- *Resource* drawback: Each process takes up memory which can become taxing. 

### Solution 2: Select
- `select()` monitors several file descriptors simultaneously, w/o needing to `fork()`
- `select()` **blocks** until at least *one* monitored file is "ready"
- A file descriptor becoming "ready" for reading means that `read()` can be called **once** w/o blocking.
	- Calling `read()` more than once can **block**
- `select()` also returns on some other conditions. (e.g., signal is received or predefined timeout expires)

#### Select Parameters
`int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);`
- `select()` returns the number of fds that are ready, or returns -1 on error.
- Set `nfds` to one or more than the highest-numbered fd of interest
	- Like if you're interested in fds 3, 9, and 50, you should pass 51 for `nfds`
	- These are bit set, allowing for macro supported bit manipulation

- `select()` takes 3 file descriptor sets as part of its input:
	- First set is monitored for *reading*
	- Second set monitored for *writing*
	- Third set monitored for *exceptions*
- These sets are *modified* by `select()` to contain only fds w/ acton of the requested type. 

#### Select and the Listener Socket
- Recall that `accept()` is a blocking call that returns after a new incoming connection is added to the listener socket's queue
- The listener socket file descriptor can be monitored w/ `select()`: It is considered "ready to read" when there is at least 1 connection queued up to be selected

### File Descriptors Sets
File descriptor sets are similar to signal sets. Use these macros to operate them:
1. FD_ZERO(): Empty the set
2. FD_SET(): Add file descriptor to the set
3. FD_CLR(): Remove file descriptor from the set
4. FD_ISSET(): Check file descriptor’s membership in a set

### Select Bottom Line
- The bottom line is that we **never** want to block any calls of `read()` or `accept()`
	- Otherwise we risk the possibility of waiting forever, even when there may be other data ready to be read from other file descriptors.
- Instead we write our client/server programs to block only on `select()`

**Debugging Tip**: Use the `strace` utility on your client/server: If it ever blocks `read()` or `accept()` then there is a bug in your program.

#### Question
Your server will be written to not block any `read()` calls, but what if it blocks on `write()`?
Answer: You should be using `select()` for both `read()` and `write()` (don't do that for milestone though)

## Level- and Edge-Triggering
### "When is a FD ready?"

Two answers:
1. *Level triggered*: When an operation (ex. `read()`) won't block
2. *Edge triggered*: When there is new action on the FD since the last time you asked.

- `select()` is level triggered
	- Meaning, if you don't read anything, `select|` will keep telling you that the FD is ready

### Limitations of Select
- `select()` has some performance limitations, and can only monitor at most `FD_SETSIZE` (1024 on Linux) file descriptors.
	- But its more portable due to this!
- There are Linux-specific (not-portable) alternatives that are more efficient
- There is a common tradeoff in the biz of *portability v. efficiency*

## Reading From Clients
- When a server does a `read()`, it is not guaranteed to get a complete line or all of the desired bytes.
	- Ex. Client could sent characters separately
	- Ex. Client could send data that is split over several segments
- If you want to operate only on full lines then the server must keep each partial line in a buffer until it gets the newline from the client.

### Buffering for Full Lines
Not generally ok to do this!
For the below code, the server should keep a buffer for each client and track the number of bytes in each buffer following the prev. message.
```C
struct client {
	int fd;
	char buf[300];
	int inbuf;
	struct client *next;
};
```

Now read bytes, check for errors, and null-terminate the finished string.
```C
void myread(struct client *p) {
	char *startbuf = p->buf + p->inbuf;
	int room = sizeof (p->buf) - p->inbuf;
	int crlf;
	char *tok, *cr, *lf;
	if (room <= 1)
	// clean up this client: buffer full
	int len = read (p->fd, startbuf, room - 1);
	if (len <= 0)
		// Clean up this client: eof or error
	p->inbuf += len;
	p->buf[p->inbuf] = '\0';
	
	lf = strchr(p->buf, '\n');
	cr = strchr(p->buf, '\r');
	if (!lf && !cr)
		return; //No complete line
	tok = strtok(p->buf, "\r\n");
	if (tok)
		// use tok (complete string)
	if (!lf)
		crlf = cr - p->buf;
	else if (!cr)
		crlf = lf - p->buf;
	else
		crlf = (lf > cr) ? lf - p->buf : cr - p->buf;
	crlf++;
	p->inbuf -= crlf;
	memmove(p->buf, p->buf + crlf, p->inbuf);
}
```

