---
layout: post
title:  "God Bless GZIP (for CAN Network Traffic)"
date:   "2024-3-5"
categories: jekyll update
---


#  Data Aquisition is expensive
An unfortunate biproduct of capitalism is that throwing bytes through a network isn't as cheap as we'd like to think it is. With companies like AT&T already piggybacking off the tower giants American Tower and Crown Castle, everybody's gotta have a piece of the pie making data strategies also a financial choice. 

When building a data acquisition system, the important questions for an engineer are 
 1. What data are we collecting?
 2. How much data are we collecting?

Specifically, we were collecting high frequency CANbus data. We ran into a issue however. We didn't know upfront what data we do and don't care about. If we filter data on edge, we lose out on potential relevant data for future uses. If we don't filter, we could be storing potential junk data that just costs unnecessary data. So unfortunately, AT&T's $$$/MB ended up being the main driver for our achitecture. 

# WTF is CAN

If you are anything like me, you came out of college thinking you were a hotshot because you understood how DNSSEC and BGP worked. You might've heard of protocls like IP, TCP, TLS, HTTPS and been confident. Little did you know, there have been more protocols invented for shit's n giggles than grains of sand in the earth. Because I am lazy, here's a ChatGPT 3.5 explanation of the protocol:

> CANbus, short for Controller Area Network bus, is a robust vehicle bus standard designed to allow microcontrollers and devices to communicate with each other in applications without a host computer. It is commonly used in automotive and industrial applications to enable communication between various components within a vehicle or a machine. CANbus provides a reliable and efficient way to transmit data between different electronic control units (ECUs) or nodes within a network, allowing for real-time control and monitoring of systems.



![standard can frame](https://canlogger1000.csselectronics.com/img/CAN-bus-frame-standard-message-SOF-ID-RTR-Control-Data-CRC-ACK-EOF.svg)

TLDR: it's the networking protocol that's responsible for making your car work. Your brake pedals don't physically connect to your tires, so something has to tell your breaks to activate. Thanks to the can-utils library, we can easily see CAN network traffic easily on a linux machine!



```bash
(1708109615.414775) can0 0CF00400#0EC17D000000007D
(1708109615.418096) can0 18FD3F00#FFFFFFFFFFFFFFFF
(1708109615.418670) can0 0CFD9200#F0FFFFFFFFFFFFFF
(1708109615.419262) can0 18F00E00#A00FFFFFEA9FFFFF
(1708109615.423085) can0 0CF00300#D20000FFFF0F1F7D
(1708109615.428077) can0 18FEDF00#7DE02E7DFBFFFFF0
(1708109615.428613) can0 18FEF000#FFFFFF0000FF33FF
```


# Compression! 

The senior engineer when faced with the problem went processing data on edge 
 - saves money by offloading computation to the edge instead of the cloud 
 - saves money by smaller transfer sizes
 - lets me play with tools i've been wanting to play more with 

 As the junior engineer, I thought lets just collect everything and try to compress it! With the way gzip works, GZIP shows better compression rates when it sees similar lines repeatedly. Since in my expereince CAN networks usually have fairly reptitive traffic, I thought GZIP'ing could be an affordable option. A further extension being stripping the timestamps due to them not being as relevant.

 ![Experiment](/images/can_compression.png)

 | Experiment | Compression Rate | 
|----------|----------|----------|
| Normal | 0x | 
| GZIP Raw | 10x | 
| GZIP Minimal | 78.9x  | 
