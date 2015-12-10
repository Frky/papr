
# Evaluating the flexibility of the Java Sandbox

By Zack Cocker

December 9th, 2015

## Motivation

Untrusted java applications running in Java sandbox. 


Question: how can we reduce the capability of malwares to escape Java sandbox.

How is the Java sandbox actually used ?

Importance of investifating how security tools are used in practice:

* design more intuitive security mechanisms
* pssible language features
* differentiate between malwares and benign code.

## Sandbox example

```
Sanboxed application --> (connect) --> Socket --> (check connect) --> Security Manager --> (check perm) --> AccessController --> (implies) --> Policy
```

### Turn off Sandbox example
```
Sandbox Application -> System.setSecurityManager(null)
```

Allowed ? 

* Yes => go do nasty stuff
* No => benign code (malware hidding)

## Study 

Investigate how benign open source Java pgms interact with the Security Manager ?

1. Benign apllications never disable the Security Manager,
2. never weaken a set Security Manager,
3. do not change a set Security Manager,
4. never modify the Security Manager if a self protecting Security Manager is set.

## Methodology:

1. Collect a large ciorpus f benign apps that use or interact w/ Security Manager
2. Static analysis to trace where the Security Manager was set & init
3. Manual code instpection
4. Dynamic analysis to verify sandbox interactions

### Security Manager interaction classification

28/36 never change the sandbox settings one the settings are initially set

## Possible answers

4 possibilities previously exposed:

1. they do
2. they do
3. they do
4. they never do

[some examples of 1, 2 & 3 given]

## Takeways from empirical study

* Applications interact with the sandbox for non security related reasions -- harder to design exploit mitigations
* Sandbox complexity & flexibility leads to insecurity

Can we differentiate benign and malicious application?
Enpirical validation

### Detection rules 

1. Privilage escalation rule: if a self-protecting Security Manager is set, a class cannot load a more privileged class
2. Security Manager Rule: one a self-protecting Security Manager is set, the manager cannot change

### Validation results
A lot of CVE_ID blocked by at least one of the two rules (all by the Security Manager rule)

Pb: large overhead due to runtime verification

## Conclusion
Importance of investigating security tools

* Improve security mechanisms
* Possible language features
* Differenciate malicious and benign code

