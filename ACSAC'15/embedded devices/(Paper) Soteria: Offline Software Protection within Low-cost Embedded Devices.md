
# Soteria: Offline Software Protection within Low-cost Embedded Devices

By Johannes Götzfried

December 10th, 2015

## Motivation

State of the Art software protection:

* based on obfuscation:
    * transformations making pgms harder to analyze
    * some pgms provably can be obfuscated (e.g. password checks)
    * some cannot be (Quines)
=> only increase time needed for analysis

software protection for Embedded Devices:
Attackers with clear economic motivations.

## Background: Sancus

### Overview

Low-cost extensible security architecture
* Strict isolation of software modules
* Secure communication and attestation
* Zero-software trusted computing base

### software Module

Memory: 

```
            | SM1 text section            |             | SM1 protected data section |
Unprotected | entry point | code & const. | unprotected |       protected data       | unprotected

Protected storage area
```

### Design details

#### PC-based access control

|From/To|Entry|Text|Protected|Unprotected|
|---|:---:|:---:|:---:|:---:|
|Entry|`r-x`|`r-x`|`rw-`|`rwx`|
|Text|`r-x`|`r-x`|`rw-`|`rwx`|
|Unprotected/other SM|`r-x`|`r--`|`---`|`rwx`|

#### Isolation can be enabled/disabled with dedicated new inst.
* protect layout, SP
* unprotect

#### Hierarchical key derivation
* K(N, SP) = kdf(KN(N), SP) (based on SP id)
* K(N, SP, SM) = kdf(K(N, SP), SM) (based on SM id)

#### Shared secret between SM on N and SP: K(N, SP, SM)

## Design

### Attacker model

Not within our attacker model:

* No DoS protection
* No HW attacks
    * RAM dumping
    * Chip probing

Within our attacker model:

* Control of all peripheral components
* Control of all software components

### Basic Idea: Offline software-Protection

We want offline software-Protection
Problem: SMs are able to access each other text section (r--)

### Problem

Code resides unencrypted withn ROM
* encrypt code within ROM
* decrypt code to RAM just before SM loading

### Loading processs
* Loader SM(L) derives K(N, SP(L), SM(L), SM(E)) = E(SM(E)) = kdf(K(N, SP(L), SM(L)), SM(E))
* Loader SM(L) decrypts SM(E) with E(SM(E)) and calls protect
* SM(L) ...

### Loading Steps of a module
Initial situation: SM(L) is active and SM(E) is encrypted

1. Loader SM(L) derives E(SM(E))
2. SM(E) gets decrypted to RAM and protected
3. SM(L) wipes data section and calls unprotect

### Security argument
* Before loading, SM(E) encrypted within ROM
* After loading: SM(E) is protected by MAL
* If SM(L) is tampered with:
    * E(SM(E) is not derived correctly
    -> authenticated decryption fails
* If SM(e) is tampered with (before (loading):
    * Integrity property is violated
    -> authenticated decryption fails
* If a reset is triggered:
    * RAM is wiped
    -> no decrypted fragments of SM(E) can be found


## Implementation

Implementation on a FPGA. Three parts.

### HW implementation
Successfully tester on the XC6VLX240T Virtex-6 FPGA

### software implementation
    * Library supporting encrypted modules
    * Fully compatible to existing modules
    * Implementation of SM(L)

### Toolchain modifications
    * Automatically identify encrypted modules
    * Transparently encrypt them
    * Host software not part of the TCB
    * Based on LLVM and pyelftool

### Encryption details

AES-128 in CCM mode of operation
    
## Evaluation

Evaluation on FPGA with core running at 20MHz.

* Overhead of Soteria compared to Sancus huge
* Power overhead of Sotaria compared to Sancus: 0.2%

### Performance

* no additional performance overhead once an app is running
* Constant overhead for resetting
* Constant overhead for protecting the loader
* Constant overhead for destroying the loader
* Overhead for loading software modules about 100ms for 1024 bytes
* Implementation of AES-128 in CCM mode has been tweaked for size

## Conclusion

Soteria as a software protection solution
* Zero-software trusted base
* Allows offline software protection
* Confidentiality of code and data before and after loading

Soteria is lightweight
* Loader module needs 200 bytes in RAM
* Only very little area and power overhead
* ...

