# Configuration Reference

Skeletons only. Swap the `<TOKENS>` for the values from your fill-in key in
`01-addressing-and-vlsm.md`.

## Base (every device — change hostname)
```
hostname R1
ip domain name lab.local
username admin privilege 15 secret Str0ngPass!
enable secret Str0ngEnable!
crypto key generate rsa modulus 2048
ip ssh version 2
line vty 0 15
 login local
 transport input ssh
service password-encryption
```

## VLANs (L3-SW + ASW1–4)
```
vlan 10
 name MGMT
vlan 20
 name SERVERS
vlan 30
 name USERS
vlan 40
 name IT-HELPDESK
vlan 99
 name NATIVE-PARK
```

## Access ports (edge switches)
Endpoints live on ASW3 / ASW4; ASW1 / ASW2 carry no endpoints (aggregation only).
```
! ASW3
interface gi1/0/5      ! Windows Server VM
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
interface gi1/0/6      ! Windows 11 client VM
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
! ASW4
interface gi1/0/5      ! Pi-Ticket
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
interface gi1/0/10     ! Pi-Monitor
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
! edge trunks: ASW1<->ASW3 and ASW2<->ASW4, both ends Gi1/0/23
interface gi1/0/23
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
```

## EtherChannel
**L3-SW**
```
interface range gi1/0/1-2
 channel-group 1 mode active
interface range gi1/0/3-4
 channel-group 2 mode active
interface range port-channel 1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
```
**ASW1 (mirror on ASW2 with gi1/0/3-4 → Po2)**
```
interface range gi1/0/1-2
 channel-group 1 mode active
interface port-channel 1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
```

## Inter-VLAN routing + DHCP relay (L3-SW)
```
ip routing
spanning-tree vlan 10,20,30,40,99 root primary
interface vlan 10
 ip address <V10-GW> <V10-MASK>
 ip helper-address <SRV-IP>
interface vlan 20
 ip address <V20-GW> <V20-MASK>
interface vlan 30
 ip address <V30-GW> <V30-MASK>
 ip helper-address <SRV-IP>
interface vlan 40
 ip address <V40-GW> <V40-MASK>
 ip helper-address <SRV-IP>
interface gi1/0/23
 no switchport
 ip address <R1L3-L3> <P2P-MASK>
interface gi1/0/24
 no switchport
 ip address <R2L3-L3> <P2P-MASK>
```

## OSPF
**R1**
```
interface gi0/0
 ip address <R1R2-R1> <P2P-MASK>
 no shutdown
interface gi0/1
 ip address <R1L3-R1> <P2P-MASK>
 no shutdown
router ospf 1
 router-id 1.1.1.1
 network <R1R2-NET> <R1R2-WILD> area 0
 network <R1L3-NET> <R1L3-WILD> area 0
```
**R2** — gi0/0 `<R1R2-R2>`, gi0/1 `<R2L3-R2>`, RID `2.2.2.2`, networks `<R1R2-NET>` + `<R2L3-NET>`.

**L3-SW**
```
router ospf 1
 router-id 3.3.3.3
 passive-interface default
 no passive-interface gi1/0/23
 no passive-interface gi1/0/24
 network <V10-NET> <V10-WILD> area 0
 network <V20-NET> <V20-WILD> area 0
 network <V30-NET> <V30-WILD> area 0
 network <V40-NET> <V40-WILD> area 0
 network <R1L3-NET> <R1L3-WILD> area 0
 network <R2L3-NET> <R2L3-WILD> area 0
```

> Export each device's real `show running-config` into `/configs/<hostname>.txt` once it's built.
