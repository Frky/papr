
# PIE: Parser Identification in Embedded Systems

By Lucian Cojocar

December 10th, 2015

## Motivation

Embedded systems are everywhere (TV, toaster, fridge, nuclear infrastructure, etc.
Common component: software.

However:

* Security mechanisms rarely deployed (e.g. no MMU => hard to separate app from OS)
* Legacy code, lack of updates
* The firmware is a binary blob
* IoT => everything is now connected to the internet

### Parser

#### Overview

* Code that deals with users' input.
__Its output is usually trusted.___
* High code complexity (e.g. parser for Turing-complete language ...)
* Manually written code
    * custom protocols
    * resource constraints

#### Definition
```
Complex code driven by external input
```

## Proposed solution

Binary solution (do not assume we have source code)

1. Transform the FW to a higher-level representation that is easy to reason about
2. Use heuristics to select ...

### Lifting the binary

We chose LLVM intermediate language => quite easy to reason about (e.g. track data).

Based on QEMU translation (which uses TCG to abstract the input binary).

```
Binary ---> TCG ---> LLVM
```

=> recursive static disassembler

#### Control flow recovery
* Functions
* Loops

#### Data-flow recovery
* Uniform memory layout
* Stack frame recovery

#### Portability
Adaptable to other platforms/architectures

### Heuristics

* Number of basic blocks
* Max number of incident edges
* number of callers
* Switch statements
* Data-flow analysis on conditional statements
* Looped switch statement

#### Analyze the structure of data-flow
We are looking for:

* hard-coded string compare functions
* Switch statements are lowered to if-then-else

Approach: 

* Compute the max *branching factor*
* Recursively on each conditional variable

Example:

```
%all = add %A, %B
%some = add %A, 0x10
%tmp = add %some, %all
%bool1 = cmp %A, %B             // increase branching factor of A and B by 1
%bool2 = cmp %all, 0x20         // idem
%bool3 = cmp %some, 0x30        // only A
% bool4 cmp %tmp, 0x40          // both
br %bcmp1, BB0, BB1
```

Our heuristic returns the maximum branching factor (here 4)

#### Analyze the structure of control-flow
What we are looking for:

* Switch statements
* Transition between states

Approach:

* Find loops
* Detect jump tables
* Hand-coded dispatch tables

### Methodology

Used these heuristics for each function and then normalize the score across all functions and then combine
all heuristics in one score
Use machine learning for finding optimal weights
Train on a set of binaries with source code

Focus on FP over TP factor

### Evaluation 

Most relevant heuristics:
* Looped switch (weight: 0.7)
* Branching factor (weight = 0.5)

|    |GPS|Meter|HDD|PLC|
|---|---|---|---|---|
|TP Rate|82%|84%|97%|26%|
|FP rate|4.7%|4.6%|19.7%|0%|

### PLC showcase

We found several interesting functions:

* Parts of proprietary storage format
* The checks of the firmware update format
* Recursive URL handling function
* Undocumented boot menu

### Firmware analysis

* Extracting FW is hard
* Share our work here embedded.labs.vu.nl
* Translator is flexible enough to implement more analysis

## Conclusion
* Showed importance of security in parsers on embedded devices
* Proposed a methodology to detect parsers and parsing like code

