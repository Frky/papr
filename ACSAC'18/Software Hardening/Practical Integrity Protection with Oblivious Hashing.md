# Practical Integrity Protection with Oblivious Hashing
*by Anahit Hayrapetyan - 05/12/18*

## Intro 

### MATE: attacker vector

* static patching of a program
* process patching/data manipulation
* tamper with execution/data manipulation

### Smart meter example

```
enum period{Peak, OffPeak, Normal};
float computeUsage(float *kwMinute, int size, enum period rate) {
    ...
    else if (rate == OffPeak) {

    }
    usage += kwMinute[i] * rating; // integrity to presever
}
```

### Obvious hashing

*Verify integrity of a program by hashing execution traces*
=> i.e., hashing memory variables that are modified during the execution of a function. At the end of the 
function execution, check hash (compliant with a possible execution trace of the function?). 

### Input dependent instructions

Compure input dependent instructions using method proposed by Scholz et al.
* data dependent insutrcitons (DDI) - at least one of its operands depends on input data
* Control flow dependent instructions (CFDI) - ...
* ....

### is not practical

* incapable of protecting input-dependent parts
* manual segregation of instructions
* performance bounds and security garantees of OH

### Contributions: practical OH
* extend OH to protect data independent instructions (SROH)
* compute input dependent instructions from literature
* protect data-dependent ...

## Short-range OH

### Idea

* Some blocks in a CFG are executed iif a sequence of mutually adjacent blocks have been executed before - and these from a path (paths may overlap)
* we introduce one hash variable for each of these paths to protect the data independ insutructions
* protect data independ instructions on these paths
* at the end, check this hash

### Example

* path identification:
...

## Conclusion

* OH verifies integrity of executed instructions, but only for input-independent instructions
* SROH also protects input-depenend instructions but only implicitly
* Tool is open-source
* ...
