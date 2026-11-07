- **UNDERLAY:** OSPF
- **OVERLAY:** None
- **VXLAN CONTROL PLANE:** Static
- **L3 gw:** BO (Bridged Overlay)
- **VLAN management:** VLAN-Based

<img width="557" height="437" alt="image" src="https://github.com/user-attachments/assets/35ed968c-bb3a-45c1-97c6-5dbdc505d45c" />

### **VLAN-VNI mapping**
#### Both SWs
```
vlan 10
  name VLAN10
  vn-segment 10010
vlan 20
  vn-segment 20010
```

### Static VNI-VTEP mapping
#### NXOS
```
interface nve1
  no shutdown
  source-interface loopback1
  member vni 10010
    ingress-replication protocol static
      peer-ip 192.168.1.1
  member vni 20010
    ingress-replication protocol static
      peer-ip 192.168.1.1
```
#### NXOS_2
```
interface nve1
  no shutdown
  source-interface loopback1
  member vni 10010
    ingress-replication protocol static
      peer-ip 192.168.1.2
  member vni 20010
    ingress-replication protocol static
      peer-ip 192.168.1.2
```
### Verify
- **View MAC table on Nexus in eve-ng:**
```
NXOS_2# show system internal l2fwder mac
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
NXOS_2# show system internal l2fwder mac
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
*    20    0050.7966.680c   dynamic   00:00:02   F     F  nve-peer1 192.168.1.1
*    20    0050.7966.6809   dynamic   00:00:02   F     F     Eth1/3
*    10    0050.7966.6803   dynamic   00:00:06   F     F     Eth1/2
*    10    0050.7966.680b   dynamic   00:00:06   F     F  nve-peer1 192.168.1.1
```
- **NVE = Network Virtualization Edge**
  - L3 engine
  - VXLAN traffic exchange endpoint
  - Services multiple VNIs
  - MP-BGP
  - ARP suppression
```
NXOS_2# show nve peers
Interface Peer-IP          State LearnType Uptime   Router-Mac
--------- ---------------  ----- --------- -------- -----------------
nve1      192.168.1.1      Up    DP        1d01h    n/a
!
!
NXOS_2# show nve peers detail
Details of nve Peers:
----------------------------------------
Peer-Ip: 192.168.1.1
    NVE Interface       : nve1
    Peer State          : Up
    Peer Uptime         : 1d02h
    Router-Mac          : n/a
    Peer First VNI      : 10010
    Time since Create   : 1d02h
    Configured VNIs     : 10010,20010
    Provision State     : peer-add-complete
    Learnt CP VNIs      : 10010,20010
    vni assignment mode : SYMMETRIC
    Peer Location       : N/A
```
- **View VNIs:**
```
NXOS_2# show nve vni
Codes: CP - Control Plane        DP - Data Plane
       UC - Unconfigured         SA - Suppress ARP
       SU - Suppress Unknown Unicast
       Xconn - Crossconnect
       MS-IR - Multisite Ingress Replication

Interface VNI      Multicast-group   State Mode Type [BD/VRF]      Flags
--------- -------- ----------------- ----- ---- ------------------ -----
nve1      10010    UnicastStatic     Up    DP   L2 [10]
nve1      20010    UnicastStatic     Up    DP   L2 [20]
```
