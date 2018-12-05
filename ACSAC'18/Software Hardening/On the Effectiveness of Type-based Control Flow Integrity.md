# On the Effectiveness of Type-based Control Flow Integrity
*by Reza Mirzazade- 05/12/18*

## Control Flow Hijacking

Memory corruption can lead to a violation of the CFG. 
* forward edges (call, jumps)
* backward edges (return addresses)

## CFI

* CFI prevents control flow hijacking by enforcing CFG at runtime
* usually generated statically using points-to analysis
    * DSA: data structure analysis
    * SVF: static value-flow (interprocedural ...)

## Runtime Type Checking
* RTC generates the CFG based on Type Signature
* RTC matches the type signature of each indirect control flow transfer with its target
* Forward edge
    * type of cuntion pointer and the tarfet are checked at each control transfer
    * Backward edges
        * type oif the callee is checked during funciton epilogue

## Reuse Attack Protector

Released in 2016, updated in 2017. 
Generates a hash of the function signature. Pb:: type collision.

## Research questions

* Can RTC be practically bypassed using type collision?
* ...

### Threat model

* attacker has arbitrary reand and write primitives to the memory
* application contains one string or multiple limited memory corruption vulns.
* DEP and ASLR are enables
* RAP is in place

## Typed ROP (TROP)

### Gadgets

* collision gadget
* linker gadget
* execution gadget

### Approach

* Building CFG, CG
* Finding gadgets
* Detecting candidate paths
* Constraing solving

=> **exploits**

## PoC Exploits (Nginx)

...

## Evaluation

* type collision
* gadget distribution
* ...

## Conclusion

* evaluated RTC from security and practically perspectives
* type collision are rare
* TROP showed collisions with other functions in a nested fashion can be exploited
* gadgets for mounting TROP ...
* ...
