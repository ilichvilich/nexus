#### LEAF_1
```
feature ospf
feature vn-segment-vlan-based
feature nv overlay
!
username admin password 5 $5$yJQ2x4lt$YzwG/j8tUvJGOm6fao0AsEPC9DupUiRcS5ZOCZMRpp
!
vlan 10
  name VLAN10
  vn-segment 10010
vlan 20
  name VLAN20
  vn-segment 20010
!
interface nve1
  no shutdown
  source-interface loopback0
  member vni 10010
    ingress-replication protocol static
      peer-ip 192.168.1.3
  member vni 20010
    ingress-replication protocol static
      peer-ip 192.168.1.3
!
interface Ethernet1/1
  no switchport
  ip address 10.0.11.1/31
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface Ethernet1/2
  switchport access vlan 10
!
interface Ethernet1/3
  switchport access vlan 20
!
interface loopback0
  ip address 192.168.1.2/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 192.168.1.2
```

#### LEAF_2
```
feature ospf
feature vn-segment-vlan-based
feature nv overlay
!
username admin password 5 $5$yJQ2x4lt$YzwG/j8tUvJGOm6fao0AsEPC9DupUiRcS5ZOCZMRpp
!
vlan 10
  name VLAN10
  vn-segment 10010
vlan 20
  name VLAN20
  vn-segment 20010
!
interface nve1
  no shutdown
  source-interface loopback0
  member vni 10010
    ingress-replication protocol static
      peer-ip 192.168.1.2
  member vni 20010
    ingress-replication protocol static
      peer-ip 192.168.1.2
!
interface Ethernet1/1
  no switchport
  ip address 10.0.12.1/31
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface Ethernet1/2
  switchport access vlan 10
!
interface Ethernet1/3
  switchport access vlan 20
!
interface loopback0
  ip address 192.168.1.3/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 192.168.1.3
```

#### SPINE_1
```
feature ospf
!
username admin password 5 $5$Y5eU7V1w$6zuwsnyMWMyyQUPsWdrxDNGid8M9kbokm6tMYRXYWz
!
interface Ethernet1/1
  no switchport
  ip address 10.0.11.0/31
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface Ethernet1/2
  no switchport
  ip address 10.0.12.0/31
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface loopback0
  ip address 192.168.1.1/32
  ip router ospf 1 area 0.0.0.0
!
router ospf 1
  router-id 192.168.1.1
```
