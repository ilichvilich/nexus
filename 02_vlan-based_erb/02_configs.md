#### SPINE1
```
feature ospf
feature bgp
feature pim
!
ip pim rp-address 11.11.11.11 group-list 224.0.0.0/4
ip pim ssm range 232.0.0.0/8
ip pim anycast-rp 11.11.11.11 192.168.1.1
ip pim anycast-rp 11.11.11.11 192.168.1.2
!
interface Ethernet1/1
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
!
interface Ethernet1/2
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
!
interface loopback0
  ip address 192.168.1.1/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
interface loopback1
  ip address 11.11.11.11/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
router ospf 1
  router-id 192.168.1.1
router bgp 65000
  router-id 192.168.1.1
  address-family ipv4 unicast
  address-family l2vpn evpn
    retain route-target all
  template peer LEAF
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
      route-reflector-client
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 192.168.1.3
    inherit peer LEAF
  neighbor 192.168.1.4
    inherit peer LEAF
```
#### SPINE2
```
feature ospf
feature bgp
feature pim
!
ip pim rp-address 11.11.11.11 group-list 224.0.0.0/4
ip pim ssm range 232.0.0.0/8
ip pim anycast-rp 11.11.11.11 192.168.1.1
ip pim anycast-rp 11.11.11.11 192.168.1.2
!

interface Ethernet1/1
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown

interface Ethernet1/2
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
!
interface loopback0
  ip address 192.168.1.2/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
interface loopback1
  ip address 11.11.11.11/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
router ospf 1
  router-id 192.168.1.2
router bgp 65000
  router-id 192.168.1.2
  address-family ipv4 unicast
  address-family l2vpn evpn
    retain route-target all
  template peer LEAF
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
      route-reflector-client
    address-family l2vpn evpn
      send-community
      send-community extended
      route-reflector-client
  neighbor 192.168.1.3
    inherit peer LEAF
  neighbor 192.168.1.4
    inherit peer LEAF
```
#### LEAF1
```
feature ospf
feature bgp
feature pim
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay
!
fabric forwarding anycast-gateway-mac 0001.0001.0001
ip pim rp-address 11.11.11.11 group-list 224.0.0.0/4
!
vlan 10
  vn-segment 5010
vlan 20
  vn-segment 5020
vlan 100
  vn-segment 5100
vlan 200
  vn-segment 5200
vlan 555
  vn-segment 5555
vlan 666
  vn-segment 5666
!
vrf context GREEN
  vni 5666
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context RED
  vni 5555
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
!
interface Vlan10
  no shutdown
  vrf member RED
  ip address 10.0.10.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan20
  no shutdown
  vrf member RED
  ip address 10.0.20.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan100
  no shutdown
  vrf member GREEN
  ip address 10.0.100.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan200
  no shutdown
  vrf member GREEN
  ip address 10.0.200.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan555
  no shutdown
  mtu 9000
  vrf member RED
  ip forward

interface Vlan666
  no shutdown
  mtu 9000
  vrf member GREEN
  ip forward
!
interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 5010
    mcast-group 239.239.239.10
  member vni 5020
    mcast-group 239.239.239.20
  member vni 5100
    mcast-group 239.239.239.100
  member vni 5200
    mcast-group 239.239.239.200
  member vni 5555 associate-vrf
  member vni 5666 associate-vrf
!
interface Ethernet1/1
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
!
interface Ethernet1/2
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
  no shutdown
!
interface Ethernet1/3
  switchport access vlan 10
!
interface Ethernet1/4
  switchport access vlan 20
!
interface Ethernet1/5
  switchport access vlan 100
!
interface Ethernet1/6
  switchport access vlan 200
!
interface loopback0
  ip address 192.168.1.3/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
interface loopback1
  ip address 192.168.10.1/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
!
!
router ospf 1
  router-id 192.168.1.3
router bgp 65000
  router-id 192.168.1.3
  address-family ipv4 unicast
  address-family l2vpn evpn
  neighbor 192.168.1.1
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 192.168.1.2
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
    address-family l2vpn evpn
      send-community
      send-community extended
!
!
!
evpn
  vni 5010 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5020 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5100 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5200 l2
    rd auto
    route-target import auto
    route-target export auto
```
#### LEAF2
```
feature ospf
feature bgp
feature pim
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay
!
fabric forwarding anycast-gateway-mac 0001.0001.0001
ip pim rp-address 11.11.11.11 group-list 224.0.0.0/4
!
vlan 10
  vn-segment 5010
vlan 20
  vn-segment 5020
vlan 100
  vn-segment 5100
vlan 200
  vn-segment 5200
vlan 555
  vn-segment 5555
vlan 666
  vn-segment 5666
!
!
!
vrf context GREEN
  vni 5666
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
vrf context RED
  vni 5555
  rd auto
  address-family ipv4 unicast
    route-target both auto
    route-target both auto evpn
!
!
!
interface Vlan10
  no shutdown
  vrf member RED
  ip address 10.0.10.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan20
  no shutdown
  vrf member RED
  ip address 10.0.20.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan100
  no shutdown
  vrf member GREEN
  ip address 10.0.100.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan200
  no shutdown
  vrf member GREEN
  ip address 10.0.200.1/24
  fabric forwarding mode anycast-gateway
!
interface Vlan555
  no shutdown
  mtu 9000
  vrf member RED
  ip forward
!
interface Vlan666
  no shutdown
  mtu 9000
  vrf member GREEN
  ip forward
!
interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 5010
    mcast-group 239.239.239.10
  member vni 5020
    mcast-group 239.239.239.20
  member vni 5100
    mcast-group 239.239.239.100
  member vni 5200
    mcast-group 239.239.239.200
  member vni 5555 associate-vrf
  member vni 5666 associate-vrf
!
!
!
interface Ethernet1/1
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface Ethernet1/2
  no switchport
  mtu 9000
  medium p2p
  ip unnumbered loopback0
  ip ospf network point-to-point
  ip router ospf 1 area 0.0.0.0
  no shutdown
!
interface Ethernet1/3
  switchport access vlan 10
!
interface Ethernet1/4
  switchport access vlan 20
!
interface Ethernet1/5
  switchport access vlan 100
!
interface Ethernet1/6
  switchport access vlan 200
!
interface loopback0
  ip address 192.168.1.4/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
interface loopback1
  ip address 192.168.10.2/32
  ip router ospf 1 area 0.0.0.0
  ip pim sparse-mode
!
!
!
router ospf 1
  router-id 192.168.1.4
!
router bgp 65000
  router-id 192.168.1.4
  address-family ipv4 unicast
  address-family l2vpn evpn
  neighbor 192.168.1.1
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 192.168.1.2
    remote-as 65000
    update-source loopback0
    address-family ipv4 unicast
      send-community
      send-community extended
    address-family l2vpn evpn
      send-community
      send-community extended
!
evpn
  vni 5010 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5020 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5100 l2
    rd auto
    route-target import auto
    route-target export auto
  vni 5200 l2
    rd auto
    route-target import auto
    route-target export auto
```
