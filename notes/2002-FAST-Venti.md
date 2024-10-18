# 2002-FAST-Venti

ShortName: Venti
Conference: FAST
FullName: Venti: A New Approach to Archival Data Storage
Tag: CS736
Year: 2002

### Keypoint

- Write-once policy
- Hash-based addressing

### Motivation

- Need for reliable archival storage
- Growth in storage capacity (allows write-once)
- Duplication in archival data

### Advantages

- Naturally coalesces duplicate blocks
- No extra space - full backup vs incremental backup
- Natural integrity checking

### Merkle Tree

- Root block point to pointer blocks
- Pointer block holds hashes of other pointer blocks
- They can hold hashes pointer block or datablock
- Changes propagates to root

### Implementation

- Data stored in RAID-5/6 (Good seq-io)
- Lookup in index
- Index is cached
- Block is also cached
- Fingerprints are in (1) index (2) with data (in header)

### Performance Problems

- Index lookup overhead
- Even in sequential reads, Venti has to access index (assuming uncached) ⇒ random access