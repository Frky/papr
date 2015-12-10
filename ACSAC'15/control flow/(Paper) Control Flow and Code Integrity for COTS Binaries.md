
# Control Flow and Code Integrity for COTS Binaries
An effective approach to defeat practical ROP attacks

By Mingwei Zhang

December 9th, 2015

## Background

Arms race between code reuse attacks and defense

* attacks: ROP, ROP-noret, JOP, JIT, ...
* defenders: CFI, CPI, Oumoron, etc.

Ensuring CFI is a principled direction against ROP.
However, not perfect.

### Observations

* Code reuse defenses are not fool-proof
* but ROP attackers' task has become much harder

### Implications on real world ROP attacks

* Finding Turing Complete ROP may be hard
    * Payload size is small
    * Limited diversity of gadgets
* So attackers will find enough gadgets to disable DEP and inject native code because native shellcode is:
    * Turing complete
    * Easy to implement
    * Mode widely available than ROP exploit code

Since code injection is attackers' target, why not build a solid defense against it?

Advantages:

* Significant challenges to ROP attackers
* Complement but orthogonal to all code reuse defenses
* Low runtime overhead
* No compatibility issues

=> secure the code loading

### Challenges to overcome

* High level: identify legitimate code modules (applications may load different set of libraries, libraries may be loaded on demand)
* Low level: securing legitimate code loading (in POSIX systems, code loading is done in user space by dynamic loader ld.so)

If a dynamic loader is corrupted, an ELF binary may be corrupted at runtime


## Case study: code injection using dynamic loader

### Background

* Loader data structure: link_map (contains data ptrs to ELF metadata ; loader frequently queries this data structure to resolve symbols and patch relocations)

* Text relocation: allows code patching (corruption) at runtime
    * Corrupt data ptrs and to values pointing attackers dynamic symbol tables and relocation table!
    * Fool dynamic loader to resolve fake symbols

Memory corruption on link_map will cause code injection attacks.

### Preparations

* corrupt loader data structure
* buffer overflow to inject malicious payload

## Proposed approach

* Intuitive approach: disallow code loading
* Refine approach: disallow code loading after program startup

In any case, dynamic loader has to stay in user memory

Our approach: secure code loading
* Permit only legitimate code modules to be loaded
* Make sure that they are never corrupted at runtime

### Technical idea
Inserting checks before code loading operations. Using CFI to ensure that none of the checks before code loading could be bypassed.

Security properties achieved:

* Module execution authorization
* End-to-end code integrity
* Multi-threading safety

### Low level policy

__Aim to secure the loading process.__

* State model
* Memory permission policy
* System calls policy

### High level policy

__Enforce module loading policy.__

Flexible policy:

* Specify trusted directories
* Specify trusted libraries
* Works with `LD_LIBRARY_PATH`/`LD_PRELOAD`

### Implementation using PSI

* Use PSI to instrument code loading logic in dynamic loader
* Use a reference monitor library to enforce policies for code loading

## Evaluation

### Performance

Average overhead: 14.20% (PSI), 14,37% (CFCI)

### Effectiveness

Block all CVE vulnerabilities involving code injection using dynamic loader.

Defeats in particular:

* Return-into-loader by making stack executable
* Statically linked binaries are also vulnerable to code injection

