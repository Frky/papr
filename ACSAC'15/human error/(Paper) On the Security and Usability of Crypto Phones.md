
# On the Security and Usability of Crypto Phones

By Maliheh Shirvanian 

December 9th, 2015

##  Security of VoIP

* VoIP communication is susceptible to eavesdropping and MITM
* Encrypt and authenticate VoIP communication without relying on third-party services

## Crypto phones

Examples: Blackphone, cryptophone, etc. 

* Secure VoIP communication based on a peer-to-peer, user-centric mechanism

### Protocol

* Requires a key exchange.
* Then a authentication using key generated

### SAS Validation

* Checksum comparison: verbally communicating and matching the short checksums displayed on user's device

* Speaker verification (by voice)

## Our contribution
* investigate the security as well as the usability of Crypto Phones as a whole 
* Design & implement a study emulating a real-world VoIP setting

* Purse extensive evaluation of benign and attack scenarios
* Investigate the effect of SAS checksum size on security & usability

### Background

#### Benign setting

1. Alice to Bob: "my code is *apple optic*" (appears on the device of Alice)
2. Bob to Alice: "my code is *apple optic*" (appears on the device of Bob) 
3. Voice verification

#### MITM on data channel

2. Bob to Alice: "my code is *atlas sugar*"

Detected because codes doe not match.

#### MITM on data + Voice channel

Should be detected because voice does not match.

### Study objectives and metrics

* Robustness (FNR, FPR on classic authentication scheme)
* Efficiently (time delay and replay rate)
* User perceptions (system usability scale, SUS) 
* Effect of checksum size (2-word vs 4-word SUS)

### Setup design and implementation
```
                                                 <---> DB
Web-based VoIP App <-- (internet) --> Web Server 
                                                 <---> Telephony soft-switch
```

### Study flow

```
Pre-study phase (demographic info) <---> Main study phase <---> Post-study phase (SUS questions)
```

### Speaker verification and checksum comparison for 2/4-word SAS

... (too fast)

### 2 vs. 4 word SAS experiment
* User are slightly more successful in detecting the morphed voice when the SAS becomes longer
* Users are less successful in checksum comparison as we move from 2 to 4 words
* The overall FPR increases when switching from 2 to 4 word SAS.

## Discussion

### Summary of results and insights

* System is usable (SUS = 75)
* FPR in detecting different and morphed voice is high
* FPR in detecting SAS mismatch is high
* ...

### Potential limitations
* Web-based application
* Possible neglectful participants
* Study is an emulation of real life study (main task of participants is not call creation)

### Potential defenses and future work

* Other SAS comparison protocols might reduce FPR, but may increase FNR
* Make the checksum comparison task more engaging for the users
* Sufficient instructions and training to those who use these applications
* Automated speaker verification and checksum comparison

## Conclusion

* We comprehensively evaluated the security and usability of mobile Crypto Phones
* Because of human errors associated with the checksum comparison and speaker verification tasks, the security of ...
* ...

(too fast)
