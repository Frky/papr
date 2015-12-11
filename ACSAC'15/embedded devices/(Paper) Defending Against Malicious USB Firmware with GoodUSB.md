
# Defending Against Malicious USB Firmware with GoodUSB

By Dave (Jing) Tian

December 10th, 2015

## Intro

One day, you find a flash drive over a parking. 

Best answer: you plug into someone else session.

We do want to analyze it, but we do not want to plug it on our own computer.

Blackhat 2014: Bad USB. Malware: the firmware of the USB Key itself => AV won't work.

Other example: USB rubber ducky (42.99$)

## BadUSB attack

### Overview of USB protocol
```
HOST                                    Device
  | ------------ SetAddr(n) -----------> |  
  | <-------------- ACK ---------------- |
  | --------- GetDesc(device) ---------> |
  | <--- Manufacturer, product, etc. --- |
  | ----- GetDescriptor(Interface) ----> |
  | <------------ Storage -------------- |
  | <--------- Human interface --------- |
```

=> this actions are invisible to the user

### 
* Malicious device controls all responses during USB enumeration
* Requests additional interfaces/drivers to be loaded on its behalf
* Device can even lie about its own identity

What went wrong ?

USB interfaces represent a set of unrestricted host permissions.

* OS knows nothing about the device __but loads drivers to make the device happy anyway)
* User knows something about the device

## GoodUSB

* Encodes user's expectations to authorize USB activity
* Tracks device's claimed identity
* Profiles suspicious devices in virtualized honeypot (let the user see what the device does)

### Design challenges

1. How can we mediate USB Enumerations?
2. How can we verify the identity of USB devices? (Find a way to retrieve the signature after first plug)
3. If a device is suspicious, what can be do about it?

### USB Mediator

GoodUSB sits between the user and the device
* Enforcing policies
* Redirecting devices to honeypot

```
User expectations (policy)  --->                    ---> USB Honeypot VM
                                    [USB Mediator] 
Storage driver             <--->                    <--- USB device
```

GoodUSB is implemented in the Linux kernel
* Identify the device firmware
* Enforcing policies at the driver level

### Linux Kernel Enhancement

* Device Class Identifier used to generate signatures for devices 
* Kernel Hub Thread Instrumentation to suspend the driver binding
* Netlink socket to communicate with user-space

### Policy Mapping

|Device|USB interface allowed|
|----|:----:|
|Flash drive|storage|
|Headset|Audio HID|
|Smartphone|Storage|
|Charger||

### Identifying USB devices

1. Registration at first plug (ask user type of device)
2. Select a security picture 
3. New plug: ask if the user recognize the picture

### Profiling USB devices

* QEMU KVM: USB device pass-thru vs. USB host controller pass-thru
* USB Monitor: a udev rule to start USB device profiling
* USB Profiler: generate a comprehensive SB device report for inspection

## Evaluation

Tested on the following devices:

* USB headset (able to disable the microphone without breaking the device access)
* USB Rubber Ducky
* Teensy 3.1
* Smartphones

### Overhead

* 1st enumeration: 5.2%
* Further enumerations: 5.0%
* Honeypot redirection: 262 microseconds

### Discussion

* Drivers
    * enforce *Least Privilege*
    * vendor specific
* Compatibility with specialized USB devices
* Usability

## Conclusion
* BadUSB attacks: unconstrained privileges
* GoodUSB: enforcing the permission model by:
    * encoding user's expectation into driver loading
    * ...

