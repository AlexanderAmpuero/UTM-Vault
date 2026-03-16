# PCRS
## Sockets

Unlike pipes and signals, **sockets** allow communication between processes running on **different machines** across a network.
### 1. Network Fundamentals

To communicate over the internet, we need to identify both the destination machine and the specific program running on it.

- **IP Address:** The machine's "street address."
    
- **Port:** The program's "apartment number." Ports 0–1023 are reserved (e.g., HTTP is 80, SSH is 22). Ports above 49151 are generally safe for custom servers.
    
- **Packets:** Data "packages" containing the payload and destination address. **Routers** move these packets through networks to their destination.

---
### 2. Client-Server Model

- **Server:** A program that sits at a specific port and machine address, waiting for messages.
    
- **Client:** A program that initiates the conversation by sending the first message to the server’s address.
    
- **Connection:** Once established, communication becomes bidirectional.

### 3. Stream Sockets (TCP)

We use **Stream Sockets** built on the **TCP** protocol.

- **Reliable:** Guarantees no lost messages.
    
- **Ordered:** Messages arrive in the exact order they were sent.
    
- **Connection-Oriented:** Requires a formal "handshake" before data flows.

---
### 4. Setting up the Socket (Common to both)

The `socket()` system call creates a communication endpoint and returns a **file descriptor**.

```C
int fd = socket(domain, type, protocol);
```

- **Domain:** `AF_INET` (IPv4 internet protocols).
    
- **Type:** `SOCK_STREAM` (for TCP).
    
- **Protocol:** `0` (use the default protocol for the type).

---
### 5. Server-Side Workflow

A server follows a four-step initialization before it can communicate.
#### **A. Bind: Assigning an Address**

`bind()` attaches the socket to a specific port and IP.

- **`struct sockaddr_in`:** The specific struct used for internet addresses.
    
- **Byte Order:** Machines store integers differently (**Endianness**). We must use `htons()` (host-to-network-short) to convert ports to "network byte order."
    
- **IP:** Use `INADDR_ANY` to accept connections from any of the machine's network interfaces.

#### **B. Listen: Preparing for Requests**

`listen(fd, backlog)` tells the OS to start queuing incoming connection requests.

- **Backlog:** The maximum number of partially completed connections allowed in the queue at once.

#### **C. Accept: Establishing the Connection**

`accept()` is a **blocking** call. It sleeps until a client attempts to connect.

- **New Socket:** On success, `accept()` returns a **new file descriptor** specifically for that client. The original socket remains open to listen for _other_ clients.

---
### 6. Client-Side Workflow

The client only needs to know the server's location to connect.
#### **A. Address Lookup: `getaddrinfo`**

Since humans use names (like `google.com`) and computers use IPs, we use `getaddrinfo()` to translate a hostname into a linked list of valid IP addresses.

#### **B. Connect: Joining the Server**

`connect()` initiates the handshake with the server’s listening socket.

```C
int result = connect(socket_fd, (struct sockaddr *)&server_addr, sizeof(server_addr));
```
---
### 7. Communicating via Sockets

Once connected, you treat the socket descriptor exactly like a file descriptor using `read()` and `write()`.

#### **Important Details:**

- **Network Newline:** Protocols often use `\r\n` (Carriage Return + Line Feed) as a standard newline.
    
- **Null Terminators:** Sockets transmit raw bytes, **not** C-strings. You must manually add a `\0` to the buffer after reading if you intend to use `printf`.
    
- **Partial Reads:** The internet is unpredictable. `write(fd, buf, 100)` might be sent in one go, but the receiver might only get 40 bytes in the first `read()` call. **Always check the return value of `read()`** and loop until you have all the data.
    
- **EOF:** When the other end calls `close()`, `read()` returns `0`.

#### **Comparison: Server vs. Client**

|**Step**|**Server Call**|**Client Call**|
|---|---|---|
|**Endpoint**|`socket()`|`socket()`|
|**Address**|`bind()` (Set my port)|`getaddrinfo()` (Find server IP)|
|**Wait/Initiate**|`listen()` then `accept()`|`connect()`|
|**Data**|`read()` / `write()`|`read()` / `write()`|
|**Cleanup**|`close()`|`close()`|

# Lecture
## Communication
### Human Communication
We used vocabulary, sentence structure, spelling, grammar, and various other rules to govern our communication.

Like would it be cool if a l l th elect ur e sli des we r e fo r ma ttt ed like thiiiiiis ?!.$

### Computer Communication
Computer communication is also governed by such rules, where,
- *Encoding* of information (text, signed/unsigned integers, floating points)
- *Ordering* of bytes matters (Like big endian or little endian)
- *Message Sequencing* (first send username, then password; order of message matters)
- *Message start and end boundaries* (like (`\r\n`) to terminate messages)

### Transport Protocols
Two wide spread model of *transport protocols* for computer communication over a net work exist as:
1. **Connectionless**: Exemplified by `UDP` protocol
2. **Connection-Oriented**: Exemplified by `TCP` protocol

Protocols are a set of *rules*. Both TCP and UDP protocols are implemented by the *Operation System*

#### UDP
- UDP is used for sending a *datagram* from one machine to another
- A *datagram* is a self-contained message w/ a beginning and end
- The OS sends the datagram, but doesn't check up to make sure that it got delivered. 

#### TCP
- TCP is used to create a *socket* (similar to pipe) to communicate between 2 processes.
	- Processes may be on the same computer, or two different computers connected by a network.
- The socket is created using a system call
- The process sending the data writes a sequence of bytes to the socket
- The OS guarantees that the bytes will be delivered over the network in the correct order to the receiving process.

### UDP v. TCP
- Comparing UDP and TCP is like comparing SMS to WhatsApp
- If you send an SMS to your friend, you have no way of knowing if the receive the message
	- (They can reply back to you confirming that they received your message though!)
- If you send a message over WhatApp, the app **tells you whether or not the message was successfully delivered**

### Internet Protocol (IP) Addresses
- An IP address identifies a specific host (computer) on a specific network
- IPv4 addressing (commonly used) identifies hosts by a 4 decimal 8-bit integer seperated by dots. (`128.100.3.30`)
- IPv6 addressing (slowly becoming more widespread) identifies hosts by eight groups of four hexadecimal digits separated by colons. ex. `fe80:1234:0432:a2d8:61ff:fe8b:8924:c23f`

#### TCP and UDP Ports
- A IP address only identifies a host, but not the program running on the host
- To communicate w/ a specific program on a host, you must specify the *port number* between `0` and `65535`
- In labs and projects, your client and server programs must use the same port number; otherwise they cannot communicate.

**Conventions**
- Ports in range of `0`-`1023` are *well known* or *reserved* (e.g., `22` for SSH, `80` for HTTP, `443` for HTTPS)
- Ports in range of `1024`-`49151` are *registered*. (e.g., `3724` for World of Warcraft)
- Ports in range `49152`-`65535` are *dynamic*
	- They're what you want to typically pick, to avoid conflicts.

### `localhost` Information
You may test your client and server program while running on the same computer, which is often used during development
- `127.0.0.1` is the "loopback" IP address, for when your program neds to communicate w/ another program on the same computer
- `localhost` is a *hostname* "aliased" to `127.0.0.1`
- To test your client and server on different PCs, obtain the PC's IP address by running `ifconfig`
	- Lab PC's have IP address in the range `142.1.X.Y`

## Pipes: Unidirectional v. Bidirectional Communication
![[Pasted image 20260309145139.png]]

**Now below we will show a socket**
![[Pasted image 20260309145257.png]]
For a socket the following must hold:
- A server must have a *listener socket* to accept new connections
- A separate socket is created to communicate w/ each client

## System Calls 
### Setting up a Server
1. `socket`: Creates a socket
2. `bind`: Assigns an address and port to the socket (must assign an IP address that actually belongs to your systems)
3. `listen`: Establish a queue for incoming connections
4. `accept` Accept queued incoming connection and creates a new socket
5. `read/write`: receive/send data on socket

### Setting up a Client
1. `socket` Creates a socket
2. `connect`: Connects to a remote server using an IP address and port
3.  `read/write`: receive/send data on socket

![[Pasted image 20260309145626.png]]

## IP Address Struct
- System calls expect the IP address to be passed in as `in_addr` struct
- There are system calls to help you convert back and forth between structs and human-readable strings. Ex:
	- Use `inet_aton` to convert IP address from a string of the form of `a.b.c.d` into an `in_addr` struct
	- Use `inet_ntoa` to convert `in_addr` struct into a string of the form `a.b.c.d`

## Endianness
- By convention, all data being set over the network must first be converted into *big endian*, known as *network byte order*
- The endianness of the host is referred to as *host byte order*
- `htonl()`, `htons()`, `ntohl()`, and `ntohs()` are used for converting between *host byte order* and *network byte order*.