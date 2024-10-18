# 2011-SOSP-iBench

ShortName: iBench
Conference: SOSP
FullName: A File is Not a File: Understanding the I/O Behavior of Apple Desktop Applications
Tag: CS736
Year: 2011

### Motivation

- Need to understand I/O pattern of complex desktop files

### Summary

- Single files (e.g. `.doc`) hides many files within it
- Seemingly sequential access pattern is not sequential
- Metadata/helper files are more frequently accessed than content itself
- Home user desire durability (frequent `fsync`)
- Applications use many threads (Cocoa library) influencing IO

### Links

- [https://research.cs.wisc.edu/adsl/Traces/ibench/](https://research.cs.wisc.edu/adsl/Traces/ibench/)