# 2013-SOSP-OptFS

ShortName: OptFS
Conference: SOSP
FullName: Optimistic Crash Consistency
Tag: CS736
Year: 2013

### Motivation

- Flushes in journaling (e.g. ordered)

![image-12.png](images/image%2012.png)

- Flushes slow down performance

### p-Consistency

![image-13.png](images/image%2013.png)

### Types of re-ordering

- Early commit
    - May replay bogus `Jm`
    - Points to garbage `D`
- Early checkpoint
    - Inconsistent in-place metadata

### Goal of OptFS

- Will not do frequent `fsync`
- Upon crash, can be reverted to a consistent stage X
- X may not be the last possible consistent stage, somewhere before that
- Trading freshness, for performance

### Technique 1: Checksums

- To remove first flush
- Include `hash(D, Jm)` to `Jc`
- If `Jc` is flushed first, then FS crashed. Replay can read `D`, `Jm` and match and ignore `Jc`

### Technique 2: Delayed writes

- To remove second flush
- Issue write of `D, Jm, Jc`
- Tell disk drive to inform OptFS when all of `D, Jm, Jc` writes are on disk
- They may be out of order, but fine, OptFS will keep `M` in memory
- Only when disk driver informs, OptFS will issue write of `M`
- No chance to write `M` before `D, Jm, Jc`

**Asynchronous Durability Notifications (ADN):** A proposed disk interface. Disk provides two notification to FS. 

1. Notification of write receipt *(I got your write-to-disk requests, on it)*
2. Notification of write durability *(I completed your request, thanks)*

### Example

![image-14.png](images/image%2014.png)

- Edges are dependencies, box ⇒ persisted, cloud ⇒ in-flight
- Deallocate journal log after `M` is persisted
- Write initiate of `Mn` `Mn+1` `Mn+2` all waits for `Dn`  `Jcn` to be persisted

### Complication 1: Data Re-use

- If a file X deallocates a datablock, another inode Y needs a block
- Only reallocate the datablock for another inode Y
    - If X’s new inode is durable (Get the ADN of X’s inode)

### Complication 2: Data Overwrite

- A files updates a datablock
- A durable Inode is already pointing to that datablock (with old content)
- Not possible to detect crash
- Solution: detect overwrite, do data-journalism here (include content in journal)

### New primitives

- `osync` ⇒ ensures ordering between writes without immediate durability
- `dsync` ⇒ ensures both ordering and immediate durability

### Advantage

- Random writes may become sequential

### Disadvantages

- Frequently overwriting (selective data journaling) can halve the throughput