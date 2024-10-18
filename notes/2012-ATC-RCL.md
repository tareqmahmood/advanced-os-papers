# 2012-ATC-RCL

ShortName: RCL
Conference: ATC
FullName: Remote Core Locking: Migrating Critical-Section Execution to Improve the Performance of Multithreaded Applications
Tag: CS736
Year: 2012

### Motivation

- Scalability issue of multi-thread system in multi-core machine
- Multiple threads, same lock ⇒ cache-line containing lock “bounces” around cores
- Cache miss on shared data
- Cannot scale with number of cores

![image-15.png](images/image%2015.png)

### Overview

- Offload critical section execution to a dedicated core
- Idea: this core with hold the lock and shared data
- No lock bouncing, less cache-miss on shared data

### Contention as a Metric

Contention: % of time spent in critical section

- Does not capture cache locality issue

![image-16.png](images/image%2016.png)

![image-17.png](images/image%2017.png)

### BG (Spinlock)

- Compare-and-Swap (CAS)
- Spin in while loop, until the value of a memory address is something else
- All core reading same cache-line that holds that memory

### BG (Ticket Lock)

- Fetch-and-Add (FAA)
- Lock has two variable (ticket, and turn)
- To acquire, atomically increase lock ticket and get the a ticket
- Then spin on lock turn to be your ticket
- On release increment lock turn so that next ticket holder can run
- Still spinning on shared data (lock turn) ❌

### BG (MCS)

- a `mcs_node` and a `mcs_queue` (a linked list of nodes waiting for lock)
- queue points to tail (last node)
- Get the tail of queue, probably my `predecessor`
- if queue is empty, I get the lock
- if queue is not empty, point the `predecessor` node’s next to me
- Spin lock on my `locked` variable (no spinning on shared variable) ✅
- When `predecessor` finishes, it updates my local `locked` variable (since it has `next` pointer)

Under low contention, CAS and MCS offer same performance

### Problem of Spinlock

- Spinning on lock wastes CPU
- Slows down thread that is holding the lock (context switch)
- Overall slow down of all threads

### BG (MCS-TP)

- If spinning for a fixed time: block/yield the thread
- Can handle more thread than MCS

### Goal of RCL

- Implement in software
- Support blocking in critical sections
- Support nested critical sections
- Support condition variables

### Algorithm

![image-18.png](images/image%2018.png)

- List of addresses serving as mailboxes
- Mailbox is split into cache line size slots
- Each slot is a request
- Client:
    - Brings a slot to cache (first cache miss)
    - Writes (1) lock to acquire (2) address of context (3) address of function
    - Waits for (3) to be NULL again
- Server
    - Loops over requests
    - Tries to read a request (second cache miss), bring data to cache
    - Function is not NULL, so begins to execute critical section
    - After execution sets to NULL (cache hit)
- Client
    - Tries to read request
    - While server core is running, request is not updates, cache hit always
    - Spinlock or Monitor to keep client waiting
    - Server core finished, (third cache miss), bring request to cache
    - Request is NULL, critical section executed

3 cache misses in total (I think they meant private core cache

### Re-engineering

![image-19.png](images/image%2019.png)

- Detecting critical section
- Wrapping in function
- Make a context structure

### When use RCL

- It has some overhead, always using it ⇒ not a good idea
- Profiler shows time spent in critical section
- If more than 20~70% time spent in CS ⇒ high contention ⇒ switch to RCL

### Disadvantages

- Single server thread ⇒ false serialization (blocking unrelated threads)
- Multiple server thread
    - Locks shared among threads of server core
    - Manage lock atomically
    - May use simple CAS lock?
    - False serialization does go down

![image-20.png](images/image%2020.png)
