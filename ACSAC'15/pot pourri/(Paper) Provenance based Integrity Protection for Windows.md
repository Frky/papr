
# Provenance based Integrity Protection for Windows

By Wai Kit Sze

December 10th, 2015

## Intro

Total number of unique malware increases exponentially

Existing defenses:

* AV (not effective against new malwares)
* Behavior monitoring
* Sandboxing/protected view/broker architecture (pb: sandbox escape attacks)

Result: sophisticated malware can chain exploits to escape all defenses

## Demo

XP VM: 

1. Connect to a malicious website (malhost)
2. Download a PDF with forms (ReadMeNow.pdf)
3. Open PDF => compromise PDF Reader and drop a second exploit in C:\

## Adobe + Stuxnet

1. Unknown sources => PDF 
1. Antivirus scanning ... Seems OK
1. => Execute it in sandbox
1. Drops its files (which is a legitimate action)
1. New file is scanned but seems OK
1. At the end, execution of injected code because of a lack of visibility from end-to-end.

## Approach: provenance tracking

1. Unknown sources => PDF 
Here the PDF is tainted as coming from untrusted source.
2. When it drops files, propagate the taint.
3. Prevent execution because it is tainted.

### How?

* Design brand new OS?
* Build mechanisms in today's OSes?
* Reuse existing mechanisms for both tracking and policy enforcement?

### SPIF

Avoid changes

* Same OSes => implemented on WXP, W7, W8, W10
* Same COTS applications (supports Microsoft Office, Adobe Reader, etc.)
* Same policy for all applications (simple, effortless policy development)
* Same user experience (minimize changes to user interactions)

### Tracking Provenance with userid

Every resource in OSes is already labeled using userid
```
System ---> benign user ---> untrusted user
```

=> creation of a untrusted userid.

PB: from OS perspective:

```
            ---> benign user 
System 
            ---> untrusted user
```
=> we need to sandbox benign and untrusted user

#### Untrusted Sandbox
confine untrusted processes from attacking benign processes directly

* No signaling/IPC with benign processes
* Cannot modify benign files

But allowed to create files and directories.

#### Benign Sandbox

* No IPC with untrusted user processes
* read/access no untrusted files

### Properties of dual-sandbox architecture
* Simple and generic policy
* Usable yet secure policy
* Preserve normal user experience (data from benign and untrusted can be saved in user dirs)

Processes can be malicious and be actively seeking ways to compromise benign processes

* run untrusted processes as a different user
* Non-bypassable & robust policy enforcement mechanism

Processes have no intention to circumvent the protection mechanism
* ...

### Implementation

* 4000 lines of C++n 1500 lines of header
* Initially developed on XP, then W8
* Compatible with all of the applications tested!


#### Microbenchmarks

Postmarks:

|File size|Benign|Untrusted|
|---|---:|---:|
|Small (< 5KB)|6.53%|12.38%|
|Medium (> 300KB)|3.05%|4.58%|
|Large (< 3MB)|1.27%|-0.62%|

Firefox loading top 1000 alexa pages => overhead of 3,62% in average for untrusted (3.32 for trusted).

### Protection against malware?

Samples from metasploit published in 2014 that can be exploited in our tested: a lot (including Stuxnet & Sandworm)


## Conclusion

* We proposed SPIF, an integrity protection system
* It requires no modification to OS, application and user experience
* It defends against high-profile malwares such as Stuxnet and Sandworm

