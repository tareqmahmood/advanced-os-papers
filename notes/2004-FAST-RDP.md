# 2004-FAST-RDP

ShortName: RDP
Conference: FAST
FullName: Row-Diagonal Parity for Double Disk Failure Correction
Tag: CS736
Year: 2004

### Motivation

- RAID-4/5 cannot handle double failures

### Background

**Failures**

- Whole-disk failure, MTTF ~500,000 hours
- Media failure, block corruption, not done in read
- Failures causes more failures
- “Scrubbing” to detect silent errors

### Example

![image-9.png](images/image%209.png)

### Reconstruction

![image-10.png](images/image%2010.png)

### Performance

- Writes: update two parity
- Good match for large sequential writes
    - Calc parity in memory
    - Works for LFS and WAFL

### Grouping

| **Algorithm Config** | **Rate (MB/s)** | **#Data Disks** | **Min Failure** | **Max Failure** |
| --- | --- | --- | --- | --- |
| RAID-4 6× (7 + 1) | 158.3 | 42 | 1 | 6 |
| RDP 6× (7 + 2) | 149.1 | 42 | 2 | 12 |
| RDP 4× (10 + 2) | 155.1 | 40 | 2 | 12 |
| RDP 3× (14 + 2) | 157.2 | 42 | 2 | 12 |

### Advantages

- Fast compute
- Easy incorporation with RAID-4/5
- Similar performance as RAID-4/5
- Un-encoded, no read overhead