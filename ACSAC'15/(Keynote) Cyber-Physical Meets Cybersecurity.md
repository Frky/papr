
# Cyber-Physical Meets Cybersecurity
Crashing Drones and Hijacked Cameras

By Dr. Jeannette Wing, Corporate Vice President, Microsoft Research

December 9th, 2015

## Intro

Smart connected devices, number growing up. Data sent to the cloud, use advanced machine learning techniques 
to analyse these data. 
We already have smart houses, hous-cleaner robots, etc. We'll have smart driving cars, embedded medical devices.
We already have physical sensors that monitor natural activity all around the world. Maybe we'll have robots as pets.
And of course, all those devices are/will be connected. And the data of a given device will affect the data of another 
device (because of a high connectivity). 

### What is common ?
They have a computanional core that interacts with the physical world. Cyber-physical systems are smarter and smarter. 
This intelligence is in software. Tends to be more and more connected.

### What could go wrong?
[demo: video of drones falling down on peaople/getting out of control]
Many companies are looking for drones as a business plan (eg Amazon delivering products
with drones). 

Trustworthiness in Cyber-Physical Systems

```
Reliability <--> Security <--> Privacy
```

Involving hardware, software and people.

## Reliability Challenges
There is research happening in this field showing that we can actually address this problem. 

Challenges:
* #1: reasoning about continuous/discrete
* #2 : uncertainty in environment
* #3 : sensors and actuators can fail

Briefly addressing challenges #1 and #2.


### Computable reals

#### A fundamentally hard problem

Fact: while the set of real numbers is uncountable, the set of computable numbers is only countable and thus 
almost all real numbers are not computable.

#### Verification challenge

* On the one hand: a real number *a* is said to be computable if it can be approximated by some computable function in the following manner: 
given any integer *n*, the function produces an integer k such that (k-1)/n <= a <= (k+1)/n

* On the other hand: computable numbers include many of specific real numebrs such as π.

### Unvercainty at multiple levels
```
High level planning <--> Correct control <--> Robust sensing <--> Secure OS
```

1. Sensors can fail
2. The control of activators can fail
3. High-level planning can be wrongly designed


System = (State  Control) || Environment

Limited battery power, not enough comp. power on board, not very robust to changes in env. or disturbances (eg wind, ...)


Safe control under uncertainty: x'(t) = Ax(t) + Bu(t)
Optimal control: minimize cost on deviation from reference + cost on control
=> minimize a function subject to the following property: we are safe (for instance).

## Security challenges
A lot of breaking news about hacking on IoT. 
One of the pbs: new attack vectors, and more of them with the growing of connectivity. Hard to know the provenance of an attack 
(and becoming harder and harder). Detecting attacks and determining root cause of attacks is harder and harder.

Minimally, we should apply best practices building secure softwares.
* Secure configuration
* Security protocols and encryption
* Secure storage
* Secure boot
* Device identity in hardware

And all of this must be built for low power and limited computing resources.

Other challenge: trust the cloud.

## Privacy challenges
Again, lot of headlines on data leaks, etc.

### Privacy is about ...
the appropriate collection and processing of info about a data subject by a data holder and the flow of info between data holders

=> hard to define formally. What does privacy mean? How do we try to protect it?

### Data Lifecycle

```
                                        --> analyze             --> insights
Data --> collection --> data management 

                                        --> share with partners --> collaborative learning

<---------                  Privacy preservation policies                       --------->
```

But a lot of places where things can go wrong. For instance, sharing with partners is compliant w/ privacy policy?
What if data is anonymized? Better ot to share data? But in this case, lose the value of collaborative learning.

How do we address: anonymization, compliance, sharing technically?

### Privacy preservation
One way to think about it:

1. Differential privacy
2. Data map
3. New encryption protocols

#### Anonymization is insufficient
Anonymized data combined across data sources can identify individuals. Example: Netflix users re-identified based on "anonymous" viewing habits.

**Anonymization does not work.**

The point is: if you have a data set with anonymized data, and if ever you can combine two kind of information, then you can very highly un-anonymize individuals.

#### What differential privacy analysis is?
Technique that enables learning about populations or population segments, while preserving the privacy of individuals
With DP the same query outputs will be observed with essentially the same probabilities, even if an individual record is added or deleted from the database

Privacy is achieved by adding noise eigther to data priori to collection or to the results of queries against pristine databases.

#### The privacy compliance challenge
Typical way: legal team -> privacy champion -> developer -> audit team. all of this by meetings.
What can we automate?
Legalease: a formal policy specification language. Legal team encode a policy to Legalease ; privay champion refines it.
Developer do some code analysis and then a checked compare annotated code w/ legalease specifications.

Legalease designed for readability. 

Grok: data inventory: annotate code + data with policy data types. Source labels propagated via data flow graph.

## How can we build cyber-physical systems that people can beat their lives on?
```
Reliability <--> Security <--> Privacy
```


