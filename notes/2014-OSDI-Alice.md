# 2014-OSDI-Alice

ShortName: Alice
Conference: OSDI
FullName: All File Systems Are Not Created Equal: On the Complexity of Crafting Crash-Consistent Applications
Tag: CS736
Year: 2014

### Motivation

- Need for crash-consistency in DB, KV Store, version controlling systems
- File systems handle crashes differently
- Even no crash consistency from FS, pushing devs to handle CC themselves
- Paper focuses on art of creating crash-consistent applications on top of varying FS

### Background (Journaling)

- Specially for in-place FS (FFS, ext3, ext4)
- Before updating FS, write notes describing update
- Once note is on disk, make actual update
- Ordered Journaling:
    - Protocol:
        - Datablock to inplace-FS
        - Tx Begin, New Inode, New Bitmap, Tx End ⇒ Journal log
        - Fsync
        - New Inode, Bitmap, to inplace-FS
        - Fsync
    - Problem: two consecutive `write` syscalls may not be ordered in reality
- Writeback Journaling:
    - Protocol:
        - Tx Begin, Metadata, Tx End ⇒ Journal
        - Concurrently flush datablock ⇒ inplace-FS
- Data Journaling:
    - Protocol
        - Tx Begin, Metadata, **Data**, Tx End ⇒ Journal
        - Fsync
        - Metadata, Data ⇒ inplace-FS

### FS Behavior

- Atomicity (does write updates both metadata and content)
- Ordering (do two writes follow order)

### App Level Consistency

- Make a copy of old data in log file (offset, size, old content)
- Fsync log file
- Fsync directory
- Modify actual file
- Fsync actual file
- Fsync directory
- Delete log file
- Fsync directory

Don’t need flushes in data-journaling mode.

Need to add checksum in writeback mode (journal could be pointing at garbage)

### Methodology

- Run user-level workload
- Record block-level trace
- Reconstruct disk states possible on power loss
- Run FS recovery after step 3 and verify integrity