
# Emerging Image Game CAPTCHAs for Resisting Automated and Human-Solver Relay Attacks

By Manar Mohamed

December 9th, 2015

## Introduction

CAPTCHA used by many websites to check either the user is a bot or a human.

Different versions:

* text-based
* image-based
* video-based

### Limitations of existing CAPTCHA

Vulnerable to automated attacks (image processing, machine learning)
Vulnerable to relay attack

## Overview of the work
Design a CAPTCHA scheme that resists both automated attacks & relay attacks by using emergence images

### Emergence images

Idea: video made up from images such that one single frame does not make any sense

## Existing solutions 

### El-based video CAPTCHA (El-Nu)
=> no security against relay-attacks

### Dynamic Cognitive Game CAPTCHA (DCG)

Challenges user to perform a game-like cognitive task interacting with a series of dynamic images.

Offer some resilience to relay attacks, but not to smart attacks (machine learning for instance)

### DCG & El-Nu

El-Nu: secure against automated attacks ; DCG secure against relay attacks
=> let's use both

## Contribution

### El-DCG design

Input image => edge mask and eight mask => El-based segment mask => pseudo 3D effect => Tile candidates => Tile mask ...

### El-DCG configurations

* Dimension: 340*400
* 3 target objects and 5 foreground objects
* Object moving speed: 3 pixels per frame
* Frame rate: 40 FPS
* 3 levels of difficulty: easy, medium, hard

### El-DCG security analysis against automated attacks

El's are robust to automatic object detection using image processing and machine learning techniques.

El-DCG: may the local density difference caused by the presence of an object?

#### Density-based automated attack resilient
In fact, no. Success rate about 0.005 (on average for easy, medium and hard)

### El-DCG Usability Study

#### Methodology
* Use El-Nu as a baseline
* Generated 100 challenges for each category of tested CAPTCHA (El-Nu + El-DCG easy, med & hard)
* Recruited 120 amazon mech. turk workers
* Employed within-subjects experiments ...
* ...

#### Results
Solving time: around 20s, significantly higher than El-Nu. Error rate lower then El-Nu actually (slightly).

### El-DCG security analysis against relay attacks

Recruited 75 MTurk workers
Low-Speed High-Latency: Error rate (usability vs. relay) around 1 ; solving time around 50s
High-Speed Low-Latency: Error rate around 0.8 ; solving time around 25s

Features to detect stream relay attacks:

* Number of drags
* Time-based
* Distance-based
* Speed-based

|        | Precision     |   Recall  |   F-measure  |
|--------|:---------------:|:-----------:|:--------------:|
|Easy    |   0.78        |   0.90    |   0.84       |
|Medium  |   0.72        |   0.87    |   0.79       |
|Hard    |   0.66        |   0.91    |   0.76       |

Success rate of relay attack is about 5%.

## Conclusion

* El-DCG CAPTCHA is the first CAPTCHA that can offer resistance to both attacks
* El-DCG CAPTCHA offers acceptable level of usability
* El-DCG CAPTCHA Offers High level of resilient against relay attacks
