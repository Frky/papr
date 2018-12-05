# TIFF: Using Input Type Inference To Improve Fuzzing
*by Herbert BOS - 05/12/18*

*Traveler bans are not a good idea...*

## Highlights

Current fuzzers are good for coverage, but find bugs by accident

New fuzzing mutation strategy:
* distinguishes between code-oriented and bug-oriented mutation
* learns input offset types which helps mutation

### Fuzzing

```
[Fuzzer] --(random input)--> [System under test] --> [Output]
```

Hope: exceptional behavior

Mutation (evolutionary fuzzing) focuses on code coverage!

### Motivation: a simple file format

```
[ ÒK | V0.1 | 04.12.18 | "Herbert\n" | \ffDD | 0010 | 0010 | AAAAAA..AA | ... ]
```

* magic `OK`
* optional data `V0.1 | 04.12.18`
* name string `Herbert\n`
* data srtructure to keep data `\ffDD | 0010 | 0010 | AAAAAA..AA`

Fuzzing:
* Magic bytes: must be fixed by the fuzzer
* Optional data: application may ignore these bytes, but fuzzer wastes time on mutating them
* Name string followed by a new line (eg Artist name): the fuzzer has to produce enough... > super hard by random mutation
* Data structure to keep data: marker -> height -> width -> data: trigger an integer overflow is hard with random mutation

### Problems summarized

* Unaware of types
   * mutated values are not appropriate 
* Unaware wether offset is even processed by the application
   * waste of mutation time
* Unaware of what and where to mutate

=> traditional byte-by-byte mutation is not a very effective strategy!

## TIFF

New mutation strategy
    * input type inference
Two aspects of coverage
    * code coverage oriented
    * bug oriented

**Type Inference Fuzzing Framework**

### Type inference

Based on how the input is used by the application (Howards, etc.) + taint analysis to know where.

### Mutation strategy

* code-coverage oriented:
    * focus on tainted bytes used in CMP instructions
    * mutate considering associated type
* Bug oriented: 
    * mutate with extreme values w.r.t. the associated type

### TIFF cycling

Inputs => execution trace => fitness function

* step 2: monitor its execution
* step 3: calculate fitness of inputs
* step 5: gather type based properties
    * type inference features derived
        * control offset (tainted cmp)
            * interested targets to change execution path
            * also compute type of such offsets (int8, int&6, etc.)
        * data offset type: 
            * type inference to associate a typ tag with offsets in input
* step 6: ...

## Does it work?

### Evaluation benchmark

* LAVA M dataset
* other set of applications

### LAVA dataset

TIFF is better than Vuzzer and much better than AFLFast, with fewer inputs, and in less time. 

### Other applications

TIFF triggers more crashes with slightly fewer inputs. 

### New bugs

* latest versions of:
    * libming-0.4.8
    * ...

## Conclusion

* Most fuzzers find bugs by accident
* Knowledge of types greatly improves fuzzing
* We can have a great coverage and trigger many bugs at the same time
* TIFF will be release as open source soon
