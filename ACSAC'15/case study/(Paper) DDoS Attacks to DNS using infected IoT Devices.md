
# DDoS Attacks to DNS using infected IoT Devices

By Ki-Taek Lee

December 9th, 2015

## Intro 

### Internet down after Cyberattack 

Nov. 29, 2014: DDoS attack: 15M packets per second.

More than 50 times normal DNS traffic (about 9M queries per minute vs. 170,000 normally).

## How to detect DDoS attacks?

Our own *Near-Real Time DNS Query Analyzing system for detecting DDoS Attack*

Analyze the DNS success rate.

### Zombie PCs? Zombie devices!

* We analyzed the IP addresses of packets and found out the cause of the attack.
* The attack came from IoT devices ...

### Strength of IoT device for DDoS

Why do attacker want to use IoT device for DDoS attack?

* Any TIME communication (on the move, night, daytime)
* Any THING communication (between computers, human to human, etc.)
* Any PLACE communication (indoor, outdoor)

## Use of IoT vulnerabilities

### Case 1. Home router

1. Tracing source IP address of DDoS and identifying the devices
2. Connecting to admin pages of home routers
3. Connecting to telnet for analysis => some weird processes were running
4. Download firmware from the home router
5. Reverse-engineer the firmware:
    * use busybox to download malware
It would spread by finding new devices using a random scan of IP address.
6. Get malwares from the distribution server and bin.sh

### Case 2. Network switches

Very similar to case 1, but something is different. 
Malicious commands analysis: containing, among other stuff, DDoS attack commands.

### Case 3. CCTV
Trace source IP address of DDoS attack and find out a management page of CCTV

* Malwares in CCTV:
    * password is changed
    * update with infected firmware (get root permissions)
* rtsp://...

Scanning 120M IP over the internet with the tool and found 23,500 CCTV IP, among them 9,000+ vulnerable ones. Default id, password are commonly used (64%).

### Amount of infected devices
1,199,552 are vulnerable among 2,000M IP addresses: 96% home routers!

## Infection flow of IoT

```
Attacker or infected IoT device --- (external infection flow) ---> IoT device --- (internal infection flow)
```

External:

1. IP range scan
2. Access to victim's IP through telnet or web
3. Attack with default id, pwd
4. upload malicious code

Internal:
... (too fast)

## Conclusion

* The internet of things is beginning to grow significantly
* IoT devices are vulnerable
* ...

### Future works
Automatic vulnerabilities scanner for IoT

