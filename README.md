# mpls-TrafficEngineering
This repo contains Cisco IOS configuration  for a dual-homed MPLS backbone lab on EVE-NG demonstrating Traffic Engineering (TE).  
<img width="2772" height="1395" alt="image" src="https://github.com/user-attachments/assets/855c74c2-a7a6-4287-b433-4f1e87c453c8" />
The configuration below uses PE1 as reference

## Enable MPLS TE
mpls traffic-eng tunnels 

router ospf 1 

mpls traffic-eng area 0

mpls traffic-eng router-id Loopback0

## RSVP bandwidth on links
interface e0/1    ! PE1 → P1

 ip rsvp bandwidth percent 75
 
 mpls traffic-eng tunnels

interface e0/0    ! PE1 → P2

 ip rsvp bandwidth percent 75
 
 mpls traffic-eng tunnels

## Explicit paths
ip explicit-path name PE3-VIA-P1 enable

 next-address (P1-facing-PE1)
 
 next-address (PE3-facing-P1)

ip explicit-path name PE3-VIA-P2 enable

 next-address (P2-facing-PE1)
 
 next-address (PE3-facing-P2)

ip explicit-path name PE4-VIA-P1 enable

 next-address (P1-facing-PE1)
 
 next-address (PE4-facing-P1)

ip explicit-path name PE4-VIA-P2 enable

 next-address (P2-facing-PE1)
 
 next-address (PE4-facing-P2)

## Tunnel to PE3
interface Tunnel103

 ip unnumbered Loopback0
 
 tunnel mode mpls traffic-eng
 
 tunnel destination (PE3-loopback)
 
 tunnel mpls traffic-eng bandwidth 50000
 
 tunnel mpls traffic-eng priority 1 1       ! high priority, cannot be preempted
 
 tunnel mpls traffic-eng path-option 1 explicit name PE3-VIA-P1 lockdown
 
 tunnel mpls traffic-eng path-option 2 explicit name PE3-VIA-P2
 
 tunnel mpls traffic-eng autoroute announce

## Tunnel to PE4
interface Tunnel104
 ip unnumbered Loopback0
 
 tunnel mode mpls traffic-eng
 
 tunnel destination (PE4-loopback)
 
 tunnel mpls traffic-eng bandwidth 20000
 
 tunnel mpls traffic-eng priority 2 4       ! setup prio 2, hold prio 4 (can be preempted)
 
 tunnel mpls traffic-eng path-option 1 explicit name PE4-VIA-P1 lockdown
 
 tunnel mpls traffic-eng path-option 2 explicit name PE4-VIA-P2
 
 tunnel mpls traffic-eng autoroute announce

 ---

Note: All routers in the TE domain (PEs and Ps) must have MPLS TE enabled, RSVP bandwidth set on core links, and OSPF configured with mpls traffic-eng area 0. Only headend PEs need tunnel and explicit-path configuration.
