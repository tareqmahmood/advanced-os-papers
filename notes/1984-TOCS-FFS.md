# 1984-TOCS-FFS

ShortName: FFS
Conference: TOCS
FullName: A fast file system for UNIX
Tag: CS736
Year: 1984

- Keeping blocks of a file close together
- Cylinder Group
- Bus was slower than reading block by device driver
    - Two consecutive blocks cannot be sent one after the other
    - Blocks of same file are interleaved in track
- “Stupid File Systems Are Better”
- Write coalescing ⇒ grouping multiple writes
- Disk scheduling algo: SSTP, SPTP, Elevator
- Improvement on Unix FS

![image.png](images/image.png)

- Problem of old-Unix FS
    - Free list disorganized
    - Small block size
    - No locality
- Extra large block ⇒ internal frag
- Fragments ⇒ sub-block, last block can share fragment with other files
- Bitmaps for tracking free blocks
- Each group ⇒ [Super][Summary][Bitmap][Inodes][Datablocks]
- Also a summary block for meta data (number of free inodes, data blocks)

![image-1.png](images/image%201.png)

![image-2.png](images/image%202.png)

- For each directory
    - All files are in same CG
        - If file is big, it also may skip to new CG
    - But subfolders may be in different CG

![image-3.png](images/image%203.png)
