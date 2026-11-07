- **UNDERLAY:** OSPF
- **OVERLAY:** None
- **VXLAN CONTROL PLANE:** Static
- **L3 gw:** BO (Bridged Overlay)
- **VLAN management:** VLAN-Based

<img width="564" height="511" alt="image" src="https://github.com/user-attachments/assets/c74a73af-d267-4949-ac4a-5e6e97bad759" />



### **VLAN-VNI mapping**
#### Both SWs
```
vlan 10
  name VLAN10
  vn-segment 10010
vlan 20
  name VLAN20
  vn-segment 20010
```

### Static VNI-VTEP mapping
#### LEAF_1
```
interface nve1
  no shutdown
  source-interface loopback0
  member vni 10010
    ingress-replication protocol static
      peer-ip 192.168.1.3
  member vni 20010
    ingress-replication protocol static
      peer-ip 192.168.1.3
```
#### LEAF_2
```
interface nve1
  no shutdown
  source-interface loopback0
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
LEAF_1# show system internal l2fwder mac
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
*    20    0050.7966.680c   dynamic   00:01:52   F     F  nve-peer1 192.168.1.3
*    20    0050.7966.6809   dynamic   00:02:04   F     F     Eth1/3
*    10    0050.7966.6803   dynamic   00:05:32   F     F     Eth1/2
*    10    0050.7966.680b   dynamic   00:05:32   F     F  nve-peer1 192.168.1.3
```
- **NVE = Network Virtualization Edge**
  - L3 engine
  - VXLAN traffic exchange endpoint
  - Services multiple VNIs
  - MP-BGP
  - ARP suppression
```
LEAF_1# show nve peers
Interface Peer-IP          State LearnType Uptime   Router-Mac
--------- ---------------  ----- --------- -------- -----------------
nve1      192.168.1.3      Up    DP        00:22:55 n/a
!
!
LEAF_1# show nve peers detail
Details of nve Peers:
----------------------------------------
Peer-Ip: 192.168.1.3
    NVE Interface       : nve1
    Peer State          : Up
    Peer Uptime         : 00:23:08
    Router-Mac          : n/a
    Peer First VNI      : 10010
    Time since Create   : 00:23:08
    Configured VNIs     : 10010,20010
    Provision State     : peer-add-complete
    Learnt CP VNIs      : 10010,20010
    vni assignment mode : SYMMETRIC
    Peer Location       : N/A
```
- **View VNIs:**
```
LEAF_1# show nve vni
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
- **View remote VTEPs used for ingress replication:**
```
LEAF_1# show nve vni ingress-replication
Interface VNI      Replication List  Source  Up Time
--------- -------- ----------------- ------- -------
nve1      10010    192.168.1.3       CLI     00:25:13
nve1      20010    192.168.1.3       CLI     00:05:56
```
