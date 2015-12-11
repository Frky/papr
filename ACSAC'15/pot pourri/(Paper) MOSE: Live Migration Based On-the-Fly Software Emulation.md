
# MOSE: Live Migration Based On-the-Fly Software Emulation

By Jinpeng Wei

December 10th, 2015

## Intro

...

## Motivation & Background
* Translation of SW written for a different HW or OS into SW that can run on current plateform
* For ex, QEMU
* Emulation has demonstrated its strength in  SW testing and other fields

### Demand emulation

* SW analysis based on emulation can incur considerable execution overhead (8x perf. degradation)
* Demand emulation has been proposed to dynamically switch a running system between virtualized and emulated

### Related work

In-host demand emulation: Alex Ho proposed an architecture that uses Xen and para-virtualization for perf QEMU for emu and taint analysis

Limitations:

* Cannot support close source guest OSes
* Cannot use modern HW virtualization extensions
* Designed only for one kind of analysis (taint here)
* Emulation and virtualization must run on the same physical host

## Architecture

Live mitigation based on the fly SW emulation

```
    [Guest VM]               |                      | [Guest VM]
    VMM                      |   <---(migrate)--->  | 
    (Full HW virtualization) |                      | Machine Emulator

                    --------- (network connectivity) -------
                                Shared Storage
```

* Can use modern HW virtualization extensions
* Capable of supporting close source guest OSes
* Analysis-flexible: instruction tracing, taint analysis, virtual machine intro, etc.
* Decouple resources intensive analysis from normal execution

### Flow diagram of a typical application

```
[QEMU (Host 1)]                             [DECAF (Host 2)]
step 1: execution
! ERROR !           ----- (step 2) --->     step 3: analysis (tracecap)
step 6: re-run      <---- (step 5) ----     step 4: patch 
```

## Prototype

Overview:

* HW virtualization plateform based on KVM shipped w/ Ubuntu 12.04 LTS
* ...

### Migration
```
suspend -> save state -> send -> load -> launch
```

Requirement for successful heterogeneous migration; ensure that a SW system (OS kernel in particular) that was initialized for and has executed on one HW remains correct and consistent when executing in a different HW config without init step

### Challenges of heterogeneous migration
* HW equivalence (same I/O behavior for all HW pieces)
* HW state equivalence (internal state of HW devices)
* SW state equivalence (state info)

### Discrepancies resolved

|   |   |
|---|---|
|HW equivalence|2|
|HW state|5|
|SW state|9|

#### Example: system management interrupt

* Observation: when migrating from KVM to DECAF, screen was restored but guest VM was non-responsive
* Cause: CPU_INTERRUPT_SMI flag to be set. KVM forgets to clear the flag because it does not bother KVM, BUT causes trouble in DECAF

## Case studies

### Kernel panic diagnosis in DECAF

* Introduce a small and efficient Fault Detector in KVM kernel module to catch NULL pointer dereference and initiate a live migration in DECAF
* Utilize DECAF plugins to inspect the execution and analyze
    * Instruction trace analysis recovers panic execution flow
    * Function hooking recovers the panic error message
        * cont_add(facility, level, text, len) --> "unable to handle NULL pointer at virtual address ..."
* Use DECAF to modify the guest's execution
    * Abort the execution of a faulting function, undo the impact of a partial execution of that function
        * global impact
        * local impact
        * the impact is summarized as changes to the guest state
        * undo actions is the reverse of the change
    * redirect the guest execution to a function that cleanly removes the offending kernel module


#### Performance measurement during a kernel panic
* Metric: throughput of an Apache server in the guest VM
* With our Kernel Panic recovery, service can continue (without: it crashes)

### Probing & patching of security vulnerabilities
* We first run an OpenSSL server w/ heartbleed
* Next we migrate the guest VM into DECAF
* Use DECAF's support for instruction tracing and VMI to locate the bug
* Use DECAF VMI support to locate the library of interest ...
* ...

### On-demand symbolic execution

* Detects the need for symbolic execution in QEMU/KVM and automatically migrate the guest VM to S2E for continued symbolic execution
* Video DEMO
* MOSE is not analysis-plateform dependent

## Conclusion

* Proposed MOSE
* Built a prototype based on QEMU
* Constructed three use cases to demonstrate the benefits of the idea:
    * kernel panic triage
    * patching
    * on-demand static analysis

