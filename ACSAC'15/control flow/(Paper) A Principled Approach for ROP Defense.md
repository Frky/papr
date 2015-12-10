
# A Principled Approach for ROP Defense 

By Song Gao

December 9th, 2015

## Intro

### ROP attacks

* an exploit technique for code reuse -> no need for code injection
* widely used in real-word exploits

### Control-Flow Integrity 

* can defect most but not all ROP attacks
    * relies on statically determined CFG
    * "advanced ROP attacks"

* Strengthening CFI
    * Forward edge (indirect calls and jumps)
    * Backward edge: returns

__Focus here in backward edge__

Main way to prevent it: shadow stack technique (to check the consistency of the returned value)

### Existing shadow-stacks

* RAD
* Shadow stack plus CFI
* ROP defender
* Lockdown
* Dang et. al.

Mainly based on source code or lead to a high overhead.
Because of compatibility, completeness and performance issues, not a large deployment of shadow-stack methods.

## Proposed approach

* A simple policy: return instructions should ... (too fast)
* Dynamic interpretation of intent
    * The ability to return to a location => one-time use capability
    ¨return capabilities must be used in a LIFO order

### Infer CFI

Calls:
* Do not always have matching returns
    * most calls are use for function invocations and express an intent to return 
    * However, callees may not exercise the return
* Not all compatibility pb but increase attackers' options

Returns:
* Do not always have a matching call
* Compatibility problem

Goal: identify such cases

### Static analysis of non-standard returns

* Non-standard returns: return without matching calls

```
Candidate snippet extraction ---> Semantic analysis --> NSR identification ---> intended control flow inference
```

#### Example

| Code snippet          | Semantic equations |
|-----------------------|--------------------|
|`movl %eax, (%esp)`    | `'(+0) = EAX`      |
|`movl 0x(%esp), %eax`  | `eax = *(+4)`      |
| `ret`                 |   `ra = '(+0)= EAX`|

### Multi-threading support

Goal: support software w/ diverse threading mechanisms
* OS-visible threads
* user-level threads

Each stack and its RCAP stack should be in sync

__Technique:__ stack pointer value change

## Implementation

### Static analysis

* PSI for disassembly
* Lift to IR for semantic analysis

### Binary rewriting

* PSI plugin
* Coarse-grained CFI as base policy
* Protecting the dynamic loader

## Evaluation

### Accuracy
... (too fast)

### Compatibility
No false-positive

### Protection
Experimental evaluation: two real-world ROP attacks tested, both are blocked

### Performance
Overhead of about 17% = 13% (PSI) + 4%

## Conclusion
* A principled approach for ROP defense
* Accurate inference enforcement program intended control flow

