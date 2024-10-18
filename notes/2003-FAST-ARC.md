# 2003-FAST-ARC

ShortName: ARC
Conference: FAST
FullName: ARC: A Self-Tuning, Low Overhead Replacement Cache
Tag: CS736
Year: 2003

### Motivation

- Existing cache replacement policies are not adaptive
    - Recency-based (LRU), Frequency-based (LFU)
- Need for dynamic cache policy, need for adapting to dynamic workload
- Need for elimination of manual tuning (LRU-2, 2Q,LRFU)
- Scan-resistant cache policy (a scan should not [entirely] poison the cache)

### Background (LRU-2)

- Tracking last two access times of pages in cache
- Evicts the page that has the oldest second-to-last access or hasn't been accessed twice yet.
- Scan resistant, one-time access resistant
- Complexity: logarithmic, need to search in priority queue, overhead of maintaining two access times
- Parameter: Correlated Information Period (CIP), for how long a page (that was accessed once) should be retained in cache. (*”What do you mean by ‘second access’?”*)

### Background (Ghost Caches 👻)

- A list of recently evicted pages
- Metadata only, no content
- Gives a chance to see if the eviction was a good idea.
- If hit on ghost cache (since no data, need to read from disk), eviction was not a good idea. Adjust params.
- Also good for estimating optimal cache size (many hits on ghost cache ⇒ need larger cache)

### Background (2Q)

- Cache is split into two queues: A1_in (single access), Am (multiple access), A1_out (ghost cache of A1_in)
- A1_in and Am is LRU
- Ratio of |A1_in| and |Am| is a param
- Initially A1_in=[] and Am=[] and A1_out = []
- Single access pages go to A1_in
- Algorithm:
    - Hit in A1_in (double access too soon) ⇒ move to front of A1_in
    - Hit in A1_out (seems like legitimate double access) ⇒ promote to Am
    - Hit in Am ⇒ move to front of Am
    - Eviction: When queue is full
        - From tail of Am, after promotion of another page
        - From tail of A1_in, when new page accessed

![image-11.png](images/image%2011.png)

- Postgres uses 2Q.

Paper: [https://www.vldb.org/conf/1994/P439.PDF](https://www.vldb.org/conf/1994/P439.PDF)

Ref: [https://arpitbhayani.me/blogs/2q-cache/](https://arpitbhayani.me/blogs/2q-cache/)

### Background (MQ)

- M queues
- Combine recency + frequency
- Each page has (1) frequency (2) expiration time
- Repeated access: promotion
- Demotion of aging pages
- Also a ghost cache

### ARC Overview

- Two lists T1 and T2
- T1 (Recency), T2( Frequency)
- Two ghost caches for T1 and T2: B1 and B2
- Total cache size = `c`
- Parameter: `p` = size of T1 (Then size of T2 = `c - p`)
- Algorithm:
    - Miss overall ⇒ Bring to T1 from disk
    - Hit on T1 ⇒ Move to T2
    - Hit on T2 ⇒ Move to front of T2
    - Hit on B1 ⇒ Bring to T2 from disk, increase `p`
    - Hit on B2 ⇒ Bring to T2 from disk, decrease `p`
- Scans: flows through T1 and B1 and gets evicted. T2 unchanged 🛡️

### Performance

- On dynamic workload
    - Outperforms LRU, LFU, LIRS
    - Comparable to tuned LRU-2, 2Q

### Advantages

- Scan-resistant
- Low-overhead
- Self-tuning
- Comparable performance to offline-tuned policies