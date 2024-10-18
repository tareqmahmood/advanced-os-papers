# 2016-FAST-WiscKey

ShortName: WiscKey
Conference: FAST
FullName: WiscKey: Separating Keys from Values in SSD-Conscious Storage
Tag: CS736, DB
Year: 2016

[1996-ActaInfo-LSMTree](1996-ActaInfo-LSMTree%201237db6fa1f7808a896ed6bdc59560bb.md) 

Let’s take advantage of SSD for LSM-Tree

- Better random I/O
- Inherent parallel channel

### Motivation

- IO Amplification:
    - Storing key and value together.
    - Compaction needs moving K+V together
    - Too many writes
    - Shortens lifespan of SSD
- SSD has better random I/O performance

### Overview

- Store K and V separately
- No need to move necessarily V for compaction
- Limits number of files in each level
- Keys are further up in LSM tree, (since no value in LSM tree)
    - Less data to read for lookup
    - Less overhead in compaction

### Performance

![image-22.png](images/image%2022.png)

- Less amplification for WiscKey
- LevelDB better in small random writes and range-query
    - LevelDB values are also sorted, sequential read on range query
    - WiscKey keys are sorted, value reading ⇒ random reading
    - Small value, not much benefit for WiscKey

### Amplification

- Write Amp = Total Data Written to Device / Data Written by User
- Read Amp = Total Data Read from Device / Data Requested by User

### Garbage Collection

![image-23.png](images/image%2023.png)

- Reading from tail of vLog
- An entry in vLog has (key_size, value_size, key, value)
- If key found in LSM tree and address of value matches, move entry to head
- Else discard
- Then move tail forward