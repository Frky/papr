
# ShrinkWrap: VTable protection without loose ends

By Istvan Haller

December 10th, 2015

## Intro

### Attacks in browsers today

* Small memory corruption used to trigger metadata corruption
* Metadata corruption used to take over whole memory space
* Assumption: the entire memory address space is compromised
* Need to stop attacker from hijacking control flow (to exit sand-box)

Attacker should never be able to exit the sandbox

### What is the state of mitigation these days ?

__CFI__ 
* Microsoft CFG
* Google VTV
* "Perfect" defenses
* Attacks on "Perfect" defenses
* ...

### CFI in Google VTV:

* VTable protection
* Covers the vast majority of indirect calls 
* Analyzes the class hierarchy to infer VTable sets
* Call-sites only allow targeting a single VTable set based on type info
* Expectation: virtual calls should only allow polymorphic variants

ptr->toString() should only target polymorphic variants of toString

#### VTables and the function pointers within
```
1. AddRef
2. DelREf       1. LogToDisk
RefCounted      Logged
        |       |    
         MyClass
```

```
VTable Pointer ---> RefCounted | AddRef | DelRef
                --> Logged
                --> MyClass 
```

Call `*(VTable ptr + 1)`

#### Limitations in VTV

Example of targets allowed by VTV at a single call-site in the Chrome browser

(...)

Dangerous attack surface contrary to the desired defense model

Unexpected limitations of VTV

```
RefCounted *ptr
ptr->AddRef() ---> [ ~RefCounted | AddRef | DelRef ]
```

also allows

```
ptr->AddRef() ---> [ ~MyClass | LogToDisk ]
```

Other example:

```
MyClass *ptr2
ptr2->AddRef()      ---> [ ~MyClass | AddRef | DelRef ]
ptr2->LogToDisk()   ---> [ ~MyClass | LogToDisk       ]
```

But allows cross call

#### Attack surface evaluation of VTV

* Based on Chrome
* Call-sites allow an average of 146 targets
* No baseline to compare against
* > 17% of call-sites allow at least 10 different function semantics (name) to be called!

## ShrinkWrap
A design and testing methodology for VTable protection

* Based on detailed analysis of multi/virtual inheritance semantics within C++
* Defines the notion of VTable inheritance
* Comprehensive testing to ensure compatibility with C++ semantics
* Testing also ensures that all allowed VTables are necessary!

RefCounted VTable set
```
[  RefCounted | AddRef | DelRef ]
[ ~MyClass    | AddRef | DelRef ]
```

Logged VTable set
```
[  Logged  | LogToDisk ]
[ ~MyClass | LogToDisk ]
```

MyClass will have 2 VTables in two separate sets.

### Testing methodology

* Generate all possible unique class hierarchies up to a particular size
* Generate call-sites that take pointers from all derived types at run-time
* Check if virtual calls pass protection
* Check if all entries used within every VTable set

### Attack surface evaluation

* Based on Chrome
* Call-sites allow an average of 27 targets (vs. 146) (<0.1% allow > 1000 targets (vs. >5% in VTV))
* no call-sites allow more than one semantic
* Designed to be the baseline for VTable protection

### Compatibility and performance of ShrinkWrap enhanced VTV

* One line of code needed to be changed in the entire Chrome source tree
* Does not work with Firefox (neither does VTV)
* Performance improvement compared to original VTV

## Looking ahead

* [Git repo](https://github.com/hal-ler/shrinkwrap)
* Further limitations in CLang variant: CFI-VPTR
* Idea: compiler pen-tester
* Involve academic/industrial researchers to evaluate implementations


## Conclusions
* Optimal VTable protection possible within the bounds of C++ semantics
* Well designed protection does not necessarily cost more than the basic one
* Code quality can restrict potential for mitigation
* Difficult to prove security, but an attempt still has to be made

