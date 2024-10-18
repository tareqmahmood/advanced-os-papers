# 2002-FAST-SnapMirror

ShortName: SnapMirror
Conference: FAST
FullName: SnapMirror: File-System-Based Asynchronous Mirroring for Disaster Recovery
Tag: CS736
Year: 2002

### Overview

- File-based mirroring
- Periodic updates in batches
- Leverages file system snapshots

### Keypoints

- Async mirroring
- Uses WAFL
- Atomic update on mirror
- Near-instant recovery from mirror
- Active map to reduce network IO

### Advantage

- Reduce write latency and network BW

### WAFL helps

- Fast snapshots
- On-overwrite, Low overhead for maintaining consistency on mirror
- fsinfo points to all metadata (inode, block allocation tables, snapshots)
- fsinfo update happens atomically
- fsinfo basically a snapshot
- No reallocation across snapshots

### Active Map

| **Snapshot local** | **Snapshot remote** | **Action** |
| --- | --- | --- |
| 0 | 0 | No transfer, free in both |
| 0 | 1 | No transfer, deleted recently |
| 1 | 1 | No transfer, allocated in both |
| 0 | 1 | Transfer, new allocation |