# 2008-FAST-DDFS

ShortName: DDFS
Conference: FAST
FullName: Avoiding the Disk Bottleneck in the Data Domain Deduplication File System
Tag: CS736
Year: 2008

### Key Point

- Summary vector: In memory bloom filter to prevent disk lookups
- Stream-Informed Segment Layout:
    - Treat file as stream
    - Segments of particular stream (file) are stored together ⇒ spatial locality
    - Suppose a file with 100 segments is backed up multiple times. With SISL, even if the file undergoes minor changes, the unchanged segments will still be stored together in the same order. When the file is modified and re-backed up, the system can efficiently deduplicate the unchanged segments by accessing them together.
- Locality preserved caching