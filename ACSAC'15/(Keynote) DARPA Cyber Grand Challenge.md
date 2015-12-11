
# DARPA Cyber Grand Challenge: Creating a League of Extra-Ordinary Machines

By Benjamin Price and Michael Zhivich

December 10th, 2015


## Intro

Talk about our experience on creating this challenge. We are representing here a larger team
that contributed to the creation of this challenge.

Could a purpose-built supercomputer play DEFCON CTF?

### What kind of CTF?

Here, attack/Defense CTF (protect your infrastructure while attacking other teams.

Three categories:

* Security (keep private info private)
* Availability (keep services up and running)
* Evaluation (attack other teams)

Team communicates through a referee. Important for two reasons:

1. The referee communicate w/ teams to test their availability.
2. All traffic from team to team is anonymized by referee.

### Defense strategies

* Network level effect: is the packet coming benign/from the referee, or is it malicious?
* Reverse-engineering to detect vulnerabilities, and then
    * Fix them on their own system
    * Try to exploit it against other teams

Cyber Grand Challenge: can computers participate (and win) a CTF?

### Chess machines

A league of their own (from MacHack Vi to Deep Blue 2 through Cray Blitz and co.)

From 70 to 77:
* chess hash tables
* iterative deepening
* ...

```
" In the past, grandmasters came to our computer tournaments to laugh. Today they'll come to watch. Tomorrow they'll come to learn. "
```

=> We aim to do the same thing with CTF.

### June 3rd, 2015: the beginning ...

#### Cyber Grand Challenge Timeline

1. Qualification phase (June 3rd, 2015)
2. Finals phase (August 4th, 2016)

## Challenges for infrastructure team

Goal: build a game that incentives improvements in automated program analysis and cyber reasoning.

```
(Challenge binary + pcap) ---> Cyber Reasoning System --> (Replacement binary + proof of vuln)
```

Problems:

1. __Alternative ecosystem__: How do we make the test corpus representative of real world challs but not tainted by prior knowledge?
2. __Real-World incentives__: How do we evaluate "patched" replacement binaries to encourage solutions that will stand up to real-world pressures?
3. __Repeatable, Scalable experiments__: How do we ensure measurement system is scalable, consistent and robust?

### Alternative ecosystem

No known protocols, no code reuse. 

#### DARPA Experimental Cyber Research Evaluation Environment (DECREE)

System calls (7):
* allocate
* deallocate
* transmit
* receive
* fdwait
* random
* _terminate

#### Scoping the problem: DECREE

Number of system calls

* Linux 3.19: 322
* W8: ... 
* DECREE: only 7

No shared libraries, no shared memory, no file system ...

Restricted and limited environment.

However, limitless possibilities:

* Basic messaging application
* RAM-based files
* ...

### Real-World incentives

Create a metric that reflects real-world priorities.

1. Security solutions should not break functionality
(AV delivers a false-positive detection in SVGHOST.exe ...)
2. Significant performance degradation will not be tolerated
Up to 10%, no impact on score. Above that, decreasing to 0 points when overhead is 100% or more.
3. Security solutions must mitigate attacks
4. Reward proof-of-concept discovery enables fixing bugs sooner

#### Scoring algorithm

Subscore(RB, PoV) = Availability(RB) * Security(RB, PoV) * Evaluation(PoV)

1. Availability: combines a functionality factor and a performance factor
2. Security: 0 if no reference PoV mitigated, 1, otherwise depending on the mitigated attack (was it patched by other teams?)
3. Evaluation: 1 if PoV was unsuccessful, 2 if was successful

Beware the cracks in the abstract layer.

#### Example: Echo service
```c
void echo() {
    char buf[64];
    while (receive(STDIN, &buf, 128, NULL) == 0) {
        transmit(STDOUT, &buf, 64, NULL);
    }
}
```

* Accessing unmapped memory (SIGSEGV)
* Executing illegal instruction

Send 128 bytes to this service - does it crash?
It depends on packet size and timing.

1. Single write, single read: crash
2. Multiple writes, single read: crash
3. Multiple writes, multiple read: no crash

This behavior is non-deterministic. How do we handle this? 
* Change CB to avoid non-deterministic behavior
* Verify reference PoVs and polls work with different write chunk sizes and random seeds
* re-run competitor PoVs several times with different random seeds (if it's ever successful => PoV is valid)

### Repeatable & scalable experiments

A lot of CRS. How do we distribute challenges? How do we get the results? How do we evaluate the submissions?

```
Team ---> Team bucket ---> FW ---> Limbo (submission validator) ---> FW ---> EVALUATION INFRA
```

* Each team has equal access to the challenge bundle
* Contents of the challenge bundle remain secret until CQE begins

1. High availability: Amazon S3
2. Each submission is time-stamped
3. Only scoring system can read submission achieved by encryption w/ pre-shared per-team keys
4. Submission uploads must be atomic and only allowed during CQE (client sends chall id, sha256 of the solution, once received: OK, then client sends the solution (even after the CQE expired, as long as the hash was received before)


#### Scoring system architecture

Submission queue, scoring taskmaster, use of VMs.


|    |Number of tests|
|----|-----:|
|Functionality & perf|16M|
|Reference sec|186,720|
|Consensus|438,760|
|PoV|52,600|
|TOTAL|16,800,000|


## Results

### Some numbers

* 131 challenge sets
* 590 embedded vulnerabilities
* 5 IPC challenge sets
* 50+ distinct CWEs
* 6,000+ functions
* 190,000+ LOC .h
* 7,000+ LOC .cc
* 200,000+ LOC .c
* 24h to solve challenges

### Common weaknesses in CQE

Buffer overflow, stack overflow, heap overflow, etc.

### CQE from perspective of CRS

1. Encrypted CQE bundle
2. Get binaries
3. Produce replacements
4. Submit results

```
challenge binary --> triage --> unpack --> program analysis --> network analysis --> defense generation
```

#### Strategies

1. Fuzz and fix
2. Generic hardening (analyze CB for possible mem corruption + patch to validate mem access)
3. Symbolic execution (collect path constraints, solve for possible mem corr., verify via concrete exec, patch)

#### Strategy distribution

* A very few PoV without patch
* Very very few PoV + patch
* A lot of successful patch

=> generic hardening was the most used strategy


### Example: YAN01_00012

Simple stack-based machine that uses 32-bits words

* 3 bits for opcode
* 29 bits for immediate values

Vuln.: missing check on writes to a heap-allocated buffer

This challenge was designed to test the ability to:
* monitor the heap
* support bitwise operations for ...
* ...

Fairly simple instructions. 

Vulnerable instruction: `DUP n` (duplicate nth stack entry and push it on the top of the stack)

#### First PoV attempt

Delay 500, and then write "A" * 4096 => does not work

#### First successful attack

```
PUSH 0
PUSH 0
PUSH 0
SUB
PUSH 0
DUP 0
PUSH 0
PUSH 0
JMPZ
```

Last instruction jumps to first => infinite loop which is stack-positive.

#### Patching

Vulnerable push instr.: pb: no check if the address where to push is in the stack.

How to patch it? Add check before pushing to stack. In fact, here the check was added at the unique exact point where it was needed, 
and pretty cleverly => impressive.

* First successful patch on any challenge after 5 minutes.
* First successful PoV after 1min54s.

### Machines think differently

PoV:

* 41% unintended (bugs first found by machines)
* 59% intended (bugs inserted by design)

For example, it turns out not reading from a socket can cause a buffer overflow if writer doesn't check available buffer space.

CRS received perfect scores on 18% of challenges.


## What's next?

"We can only see a short distance ahead, but we can see plenty of that needs to be done." - Alan Turing

* In 8 months: CGC Final Event
* 1-3 yrs: Analysis tools
* 10 yrs: Auto-custom patching
* 20 yrs: Game Theory

### Short-term

Assisting the software analysis

* Automated unpacking
* Vulnerability discovery
* Taint tracing
* Anomaly detection

### Mid-term

Custom-patching

* On-demand custom patching
    * reduce time to patch
    * not dependent on vendor
    * tailored to specific workload/inputs
    * update unsupported legacy software

* Use CRS to remove/modify functionality
    * remove remote tracking
    * don't load images in email client
    * don't turn URLS into links

Side effect: software diversity prevents widespread attacks

### Long-term

Machine vs. machine competition adds complexity and a "Game theoretic" aspect where CRS may:
* make decisions on what type of patch to deploy
* learn what kind of analysis is being used
* intentionally misinform opponent
* set up weaker defenses to see how opponent reacts

Could a CRS build an adversary profile based on visible artifacts?

## Conclusions

```
ENTER _start
    call main 
    pushl %eax
    *call _terminate*
END _start
```

(Geek joke)

### Lesson 1
```
Relevant solutions require real-world constraints.
```

* security solutions cannot break functionality
* significant perf. degradation will not be tolerated
* must mitigate attacks

### Lesson 2
```
There's no substitute for the real event
```

* Integration and scale issues are hidden until you go alive
* Practice like you play

### Lesson 3
```
Beware the cracks in the abstraction layer
```

* Low-level artifacts can affect determinism of higher-level behavior
* Resources are finite

### Lesson 4
```
Don't trust. Verify.
```

"Be conservative in what you do, be extremely conservative in what you accept from others." - CGC mantra

* Be explicit in specifications, validate ruthlessly
* Solve the halting problem (watchdog timer)

### Lesson 5
```
If you can't repeat it, it didn't happen (if it's not automated, you can't repeat it)
```

* Automated unit tests for everything
* Building and testing chall sets
Scoring cluster provisioning and push-button scoring

### Lesson 6
```
Give people a challenge, and they will surprise you
```

"Machines take me by surprise with a great frequency." - Alan Turing

## CGC Artifacts

* [Source code](https://github.com/CyberGrandChallenge)
* [Packages, VMs and Scoring data](http://repo.cybergrandchallenge.com/)


