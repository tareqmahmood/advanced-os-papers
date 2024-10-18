# 1974-CACM-Monitors

ShortName: Monitors
Conference: CACM
FullName: Monitors: an operating system structuring concept
Tag: CS736
Year: 1974

### Monitors

- An object designed to be accessed from multiple threads.
- The methods of a monitor object will enforce mutual exclusion
- One thread may be performing any action on the object at a given time.
- If one thread is currently executing a member function of the object then any other thread that tries to call a member function of that object will have to wait until the first has finished.

### Semaphore

- Lower level object
- May use semaphore for monitors
- Controls numbers of threads within critical section
- Requires manual

### Advantage of Monitor over Semaphores

- Encapsulation of shared data and synchronization mechanism
- Better dev experience, less likely to make errors

### Procedures of Monitors

- `entry` - grabs lock
- `internal` - assumes lock is held
- `external` - does not grab lock

### Conditional Variables

- Bad naming
- A way to queue threads, wake one of them up
- `queue` `wait`
- `signal` `notify`
- `broadcast` `notify_all`

### CV vs Semaphore

- Semaphore increments and decrements
    - Maintains a state
    - Past increment/decrement will effect future
- CV sends signals
    - If a thread is not queued before signal
    - It will miss the signal
    - Once signal is gone, no more effect

### How about no preemption in critical section

Then we don’t need locks

- Bad idea
- Complete control to process, malicious intent?
- Interrupts cannot be served
- Page faults cannot be served
- No multi-processor

### Hoare Semantics

- `wait` releases monitor lock
- `signal` wakes up longest waiting thread
- Assumption:
    - waiting threads runs immediately, stopping who signaled
    - no need to recheck waiting condition