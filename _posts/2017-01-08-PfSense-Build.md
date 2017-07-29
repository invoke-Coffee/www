---
layout: post
title:  "Pfsense build"
date:   2017-01-08 00:00:00 +0000
categories: pfsense
---
I think many people like me have been eyeing these Chinese made small x86 boards as potential routers.

Well i decided to be one on the brave and give one a shot. I have not seen many posts discussing these so I decided to add my voice to the discussion.

## Requirements
I wanted something small and quiet. An old pc would work fine but i would rather avoid the noise and power draw of one.

I just got a 1gb internet connection so this is going to be the most difficult challenge. I know that i won’t be able to add every rule and feature in and still expect to run at 1gbs. But I am hopefully going to be able to maintain a reasonable throughput.

I will be running pfsense and hopefully some additional plugins.

## The hardware
**QOTOM-Q190G4-S02 2016**
Onboard CPU: Intel Celeron Processor J1900 Quad-Core 2M Cache, 2 GHz, up to 2.42 GHz
Power Consumption: 10W
Memory: 1 x Memory Slot, Support 2G/4G/8GB 1333/1600MHz DDR3 SO-DIMM Memory
Hard drive: 1 x mSATA SSD slot;1 x SATA HDD Slot
LAN: 4 x RJ-45 port – 4* Intel WG82583 – 10/100/1000 Controller
https://www.amazon.com/dp/B01KX9OU58/ref=cm_sw_r_tw_dp_x_kT5rybYSVKDAD

**Kingston Digital 60GB SSD mSATA (6Gbps)** https://www.amazon.com/dp/B00COFMPAM/ref=cm_sw_r_tw_dp_x_K15ryb8MFSJAD

**Crucial 8GB Single DDR3L 1600 MT/s (PC3L-12800) SODIMM** https://www.amazon.com/dp/B006YG8X9Y/ref=cm_sw_r_tw_dp_x_S15rybN652DM6

Both the SSD and ram are over specked for this build. But the price was right so the extra over head is nice to have.


## Build

As simple as it can be open up the box and slot in the SSD and ram.

You can use a 2.5″ Sata Drive if you would like. But I went with a mSATA.

The single Dimm slot maxing out at 8Gbs is a limitation. But for PFsense this should not be an issue.

## Installing pfsense
Again a very simple the NICs show up as expected and install went smooth.

## Configuration
My isp centurylink uses both a ppoe username/password and a vlan tag on their routers. I could have set up that router to pass through to my router. But no reason to add an extra box if I don’t need to.

Additionally I have installed pfBlockerNG and pulled in some of lists. I am planning to do some more extensive things latter, But started easy.

## Performance tests
I did test to some online tests but I wanted a cleaner test.

For anyone looking to test a Gig internet connection I have found DSL reports to have the best one that I have used.

I ran Iperf3 between to systems one inside the firewall one out.  This is not a perfect test But I wanted to ensure that I can get the raw through put.

I was easily able to put 900mbs through the Firewall. I will want to keep an eye on this as I add more and more services but this is sufficient for my goals.

## Conclusions
I am really happy with this setup. It is inexpensive silent and small.

For anyone that has been eyeing these types of boards this is an good choice.
