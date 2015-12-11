
# Privacy-preserving Virtual Machine

By Kartik Gopalan

December 10th, 2015

## Introduction

Problem addressed: private data lives "forever" in the cloud

```
Application
-----------
 Guest OS 
-----------
Hypervisor
```

* Users have expectations about data lifetime
    * credit card number will be forgotten
    * unencrypted password or keys will be deleted from memory
    * ...

* Hypervisors access guest memory in unanticipated ways
    * eg VM checkpointing stores a persistent snapshot of VM's memory "forever"
    * other examples: VL introspection, forensics, logging, IDS, live migration, etc.


### Key problem

Have hypervisors do not have tools to id and exclude private data in VMs
VMs unable to specify which data is private

### Assumptions

* Guest is trusted
* Hypervisor and its tools are trusted
* Third parties are not trusted

### Possible solutions
* encrypt filesystem to store checkpoints
    * Not useful if checkpoints will be distributed
* encrypt the checkpoint
    * still extends the lifetime of confidential data beyond user's expectation
* encrypt only the confidential data and throw away the key
    * hard to track where confidential data ends up in the VM
    * taint tracking has high overhead
    * may need application modification
* VM-based sandboxing
    * focus on limiting application capabilities
    * high VM creation and memory overhead

## Solution: Privacy-preserving VM

### Overview

Use VM to isolate confidential application's memory footprint

Properties:
* lightweight
* transparent (no changes to confidential applications)

### Architecture

* PPVM "VM-forked from the primary VM (either on-demand or at primary VM's boot time)
* Confidential apps execute in the PPVM (launched via a confidential shell)
* Regular apps run in the Primary VM
* Confidential apps remain under the control of the Primary VM's root

#### Launching PPVM

Agents and guest kernel modules in Primary VM and PPVM coordinate:
* VMFork
* Application launch
* Application control
* VM restoration

#### Background: memory-translation for VMs
```
VA Space ---(GPT)---> GPA Space ---(EPT)---> PA Space

With:
- VA: Virtual address
- GPT: Guest Page Table
- (G)PA: (Guest) Physical Address
- EPT: Extended Page Table

```

* Guest sees guest physical address (GPA) space instead of physical addresses
* Extended Page Table (EPT) page fault handling: KVM uses host page table to retrieve the page frame number and adds the mapping to EPT

#### Memory copy-on-write
Memory pages of both VMs are mapped to the same physical page frames and marked copy-on-write:
    * mark the host page table and the EPT entries of the Primary VM as ReadOnly
    * Build the host page table for PPVM
    * Increase the page map count of the page by one

#### Disk and Network
* Disk
    * PPVM can be configured with its own COW disk image
    * Copying the Primary VM disk image to PPVM is expensive
    * We use Btrfs FS to clone the Primary VM image
* Network
    Primary VM and PPVM use the same:
    * externally visible IP and MAC addresses
    * different internal IP/MAC on a common subnet

### Limitations

* Upon restoration from a checkpoint, the restored VM must handle the absence of PPVM
* No data transfer from PPVM to Primary VM
* At VMFork time, parent can"t have processes whose clones in PPVM may cause conflicts
* No separate display service for PPVM

## Evaluation

### Effectiveness of Memory Isolation: MySQL

Run the MySQL server and execute the query `SELECT * FROM EMPLOYEE` 
* data present in checkpoint when executing MySQL server from Primary VM
* data absent in checkpoint when executing MySQL server from PPVM

### Memory overhead

Overhead of about 10% in term of memory usage => lightweight

### Launch time vs. Memory Usage

* Memory copy-on-write setup time increases (lin.) when memory usage of Primary VM increases
* VM stop time CPU and I/O migration time and network setup time in ...
* ...

### Launch Time vs. number of VCPUs

...

### Memory R/W performances

Using sys bench

Allocate memory of given size and perform R/W => 11% overhead with PPVM

## Conclusion

* Hypervisors need to support guest privacy protection techniques
* Current virtualization provide inadequate support to identify and isolate private data
* PPRM isolates a confidential application's footprint in a VM

