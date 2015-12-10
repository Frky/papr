
# Defeating ROP Through Denial of Stack Pivot

By Aravind Prakash

December 9th, 2015

## Background: ROP

### Basic idea 

With DEP, harder to inject code and directly execute it. 

With ROP, attackers identify what is called *gadgets*, which are
a few instructions used at some point of the program. 

Using those gadgets, an attacker could construct a whole sequence that 
becomes the payload of the attacker. Next thing to do: jump to the payload.

Problem of the attacker: *can we put on the stack the successive addresses of the ROP payload for it to
automatically execute once we jump on it?*

### ROP stages

0. Find some gadgets and combine the payload from them
1. ROP payload injection
2. Exploitation
3. Stack pivoting to jump on payload
4. Gadget-chain execution

### Defensive strategies

* Gadget hiding/elimination
* Payload analysis
* CFI (with/out source code)
* Gadget execution detection

Here, we'll talk about the last item.

## Contribution

### ROP vs. Normal execution

|   | Normal execution | ROP |
|---|-----|---|
|Instruction|defined by the HW instruction set|gadgets|
|Execution order|controlled by eip | controlled by stack pointer|
|Execution integrity|defined by integrity of instruction pointer | defined by integrity of stack pointer|
|Sensitive instructions | Indirect control-transfer inst.|inst. that alter stack pointer|

### SP Update instructions

SP-Update inst. 
* Implicit (`push eax`, `pop ebx`)
* Explicit 
    * Relative (`add esp, 0x1`)
    * Absolute (`xchg eax, esp`, `pop esp`)
```
------------ stack pointer mobility ---------->
```

### Integrity of SP
Stack localization: for any given thrad of execution, the SP always lies within the stack region of the thread

```
Stack Base < SP < Stack Limit
```

### PBlocker
* LLVM-based approach. Implements a MachineFunction pass and a runtime
* For each absolute SPU instruction, assert StackLocalization after the instruction

__Stops out-of-stack pivoting__

Principled Approach for ROP Defense

## Evaluation

### Advantages over CFI:
* fewer absolute SPU instructions than indirect control-transfer instructions
* defeats most browser attacks (e.g. heap spray) with low overhead
* Intra-modular protection => incremental deployability
* Does not rely on ASLR to hide gadgets

### Performance
Very low overhead:
* 0.7% on Coreutils
* 1.9% on Binutiles
* Main reason: fewer absolute SP update instructions

### Use of absolute SPU instructions

Mainly `echg eax, esp`.

### SPU instructions vs. Gadgets

Very few ones in fact.

|   |#SPU inst.|
|---|---|
|Coreutils|26|
|Binutiles|129|
|Libc|391|

## Limitations and future work

```
                                ---> outside stack (done)
        ---> stack pivot    ---| 
       |                        ---> inside stack (todo)
ROP ---|        
       |
        ---> pivot-less (todo)
```
    
* Intra-stack pivoting is not detected.
* Non-stack-pointer based code-reuse attacks [Schuster S&P'15]


