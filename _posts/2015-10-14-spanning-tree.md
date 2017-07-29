---
layout: post
title:  "Spanning Tree : Root Bridge Election Process"
date:   2015-10-07 00:00:00 +0000
categories: cisco
---
STPs root bridge election is something that I have to look up every time I need to look up every time that I need to work with it. So here are some quick tips for working with root Bridge election. This will show examples from Cisco switches but the concepts will be the same for any switch manufacturer.

## Bridge ID
The Bridge ID or BID is the identification number of every switch in the Spanning-tree topology.

The Bridge ID is broken up into 2 parts.

The first 2 Bytes are the bridge priority. This is a configurable value that is most commonly used to decide on the root bridge. With a default value of 32768, as always this number makes much more sense in binary 1000000000000000.

The second part is the 6 Byte Mac address of the switch. This normally can not be changed.

| 2 byte Bridge Priority | 6 byte Mac address |



If MST, PVST or PVTS+ is used the Bridge priority is further divided into a 4 bit priority and a 12 bit extended system ID. The extended system ID will be set to the Vlan that the BID corresponds to. The first 4 bits remain settable. This is commonly done by restricting setting the Bridge priority to only be incremented in 4096 bit increments. (Remember you can normally have vlans from 1-4096)

| 4 bit priority | 12 bit extended system ID | 48 bit (6 byte) Mac address |

## Election process
The switches communicate using Bridge Protocol Data Units. The switch with the Lowest BID wins and is elected as the root bridge

Is practice an admin would decrement the Bridge priority on the switch that should be the root bridge. If there is a secondary root bridge this switch would also be changed to be in between the default and primary root.

Best practice is no to use the extreme ranges of the Priority Values. Choice a value in the middle of the range this will give you flexibility in the future.

## Verification commands.
For Cisco devices some good commands to use are
```
show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    24577
             Address     00D0.D3E6.9838
             Cost        19
             Port        1(FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.BCB5.B556
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
—————- —- — ——— ——– ——————————–
Fa0/8            Desg FWD 19        128.8    P2p
Fa0/1            Root FWD 19        128.1    P2p
Fa0/24           Altn BLK 19        128.24   P2p
```
This will show you the current Root, this switches and the root Bridges BID, and the Spanning tree state of the switches ports.
```
show spanning-tree vlan 333
show spanning-tree interface g3/2 detail
```
Can also be useful.
