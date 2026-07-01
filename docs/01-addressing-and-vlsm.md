# Addressing & VLSM

**Base block:** `192.168.10.0/24`

## Requirements

| Segment | VLAN | Hosts needed | Prefix | Network | Gateway | Broadcast |
|---------|------|-------------|-------------|--------------|--------------|-----------|
| Users | 30 | 50 | /26 | 192.168.10.0 | .62 | 192.168.10.63 |
| IT / Helpdesk | 40 | 25 | /27 | 192.168.10.64 | .94 | 192.168.10.95 |
| Servers | 20 | 12 | /28 | 192.168.10.96 | .110 | 192.168.10.111 |
| Management | 10 | 10 | /28 | 192.168.10.112 | .126 | 192.168.10.127 |
| Link R1–R2 | — | 2 | /30 | 192.168.10.128 | n/a | 192.168.10.131 |
| Link R1–L3SW | — | 2 | /30 | 192.168.10.132 | n/a | 192.168.10.135 |
| Link R2–L3SW | — | 2 | /30 | 192.168.10.136 | n/a | 192.168.10.139 |

## Fill-in key (used by every config block)

Complete this once after subnetting. The build guide and `02-configuration-reference.md`
reference these exact tokens.

```
# per VLAN — network / mask / OSPF wildcard / gateway (the SVI) / DHCP range
<V10-NET> <V10-MASK> <V10-WILD> <V10-GW>   DHCP <V10-DHCP-START>-<V10-DHCP-END>
<V20-NET> <V20-MASK> <V20-WILD> <V20-GW>   (servers static — no scope)
<V30-NET> <V30-MASK> <V30-WILD> <V30-GW>   DHCP <V30-DHCP-START>-<V30-DHCP-END>
<V40-NET> <V40-MASK> <V40-WILD> <V40-GW>   DHCP <V40-DHCP-START>-<V40-DHCP-END>

# point-to-point /30 links — network / wildcard / each end's host IP
<R1R2-NET> <R1R2-WILD> <R1R2-R1> <R1R2-R2>
<R1L3-NET> <R1L3-WILD> <R1L3-R1> <R1L3-L3>
<R2L3-NET> <R2L3-WILD> <R2L3-R2> <R2L3-L3>
<P2P-MASK>        # /30 in dotted form

# hosts you place by hand
<SRV-IP>          Windows Server (VLAN 20)
<PI-TICKET-IP>    GLPI (VLAN 20)
<PI-MONITOR-IP>   Uptime Kuma (VLAN 10)
<ASW1-MGMT> .. <ASW4-MGMT>   access-switch mgmt SVIs (VLAN 10)
```

> No answer key. If you want to check your maths, plug your block into any subnet
> calculator — but doing it by hand is the point.
