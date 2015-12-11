
# DynaGuard: Armoring Canary-based Protections against Brute-force Attacks

By Theofilos Petsios

December 10th, 2015

## Background

### Stack-smashing protection

Prevents the overwrite of the return address by a stack buffer overflow:

* Place a random value after critical data in the stack frame
* Generated dynamically at the creation of each thread and stored in Thread Local Storage (TLS) area
* Check at each function epilogue
* Supported in GCC, Microsoft VS and LLVM

### Canary brute-force

An attacker may brute-force the canary byte by byte in a very few attempts:

1. Fork process => child process inherit data from parent process, including canaries (although they should be different
from a security point of view)
2. Overflow until the first byte of the canary
3. If child does not crash => byte is OK
4. Otherwise, repeat from 2.

=> canary bruteforce by overflowing the first byte, and then the second one, etc.

1024 attempts ion average for 32 bits canary and 2048 for 64 bits.

### Canary bruteforce guessing timeline

* 2006: Ben Hawkes introduced the technique 
* 2010 Adam Zabrocki discussed remote stack exploitation using this technique
* 2013 ...
* ...

## DynaGuard 

### Design

Key idea: __Upon each `fork`, and before any execution of the child process, change canary values in both TLS and all inherited stack frames.__

Require to keep in mind all canaries (per thread) pushed at some point on the stack => Canary Address Buffer.

Two flavors: Compiler-based and DBI-based

### Compiler-based

DynaGuard as a back-end chain of gcc

* Two components:
    * GCC plugin
    * Position Independent Code runtime library

* Maintain two canaries at runtime
    * DynaGuard compiled code uses DG canaries
    * Legacy code uses gcc canaries

Both canaries have same entropy (generated in the same way)
GCC plugin replaces the glibc canaries w/ DG canaries

#### Requirements

1. instrument `push`/`pop` of canaries to update CAB (gcc)
1. Hook `fork` and update canaries in child process (dynlib)
1. Intercept all stack unwinding related calls to ensure integrity of CAB (dynlib)

### DynaGuard runtime library

PIC module loaded via LD_PRELOAD

* allocates Canary Address Buffer (CAB) in the heap
* updates CAB when needed
* hook `fork` and stack unwinding

Constructor routine allocates CAB in main thread
* Hooks `pthread_create`, `start_routine`, `fork`, stack unwinding routines
* Write-protects the last page of CAB
    * When CAB is full, last write triggers `SIGSEGV`
    * Signal handler hooked to check and resize (if needed) the CAB
    * If signal is due to anything else, forward it to classic signal handler

### DBI-Based version

* Implemented using Intel's Pin DBI framework
* No source code needed
* Same design as previously except now execution occurs under Pin

#### Methodology
1. Monitor all canary `push`/`pop` operations
2. Update canaries in the child process accordingly at `fork`

#### Advantages

* No need for complex tracking of stack unwinding
* Maintain a per-thread CAB buffer, eliminating the overhead of using the PIN built-in trace

## Evaluation

### Effectiveness
Successfully defends against BROP

### Performance

* SPEC CPU 2006 INT benchmarks
* Average overhead 1.2% in GCC version, 2.92% on top of Pin in DBI version

## Summary

* DynaGuard protects canary-based defenses against byte-to-byte bruteforce of the canary cookie
* Supports applications for which source ode is available as well as binary-only pgms
    * Offers a lightweight solution for the more general problem of memory duplication with respect to reduced entropy for security-sensitive applications (e.g. PRNGs on OpenSSL and LibreSSL)
* Has minimal incremental overhead over the respective underlying protection (e.g. GCC's SSP & Pin's native DBI respectively)
* [Github repo](https://github.com/nettrino/dynaguard)
