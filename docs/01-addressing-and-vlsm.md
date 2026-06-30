# Addressing & VLSM

**Base block:** `192.168.10.0/24`

This is yours to design. Order the requirements largest-to-smallest, then carve from the
top of the block. Nothing else in the repo assigns addresses — the configs use tokens that
point back at the key you fill in below.

## Requirements

| Segment | VLAN | Hosts needed | Your prefix | Your network | Your gateway | Broadcast |
|---------|------|-------------|-------------|--------------|--------------|-----------|
| Users | 30 | 50 | ____ | ____ | ____ | ____ |
| IT / Helpdesk | 40 | 25 | ____ | ____ | ____ | ____ |
| Servers | 20 | 12 | ____ | ____ | ____ | ____ |
| Management | 10 | 10 | ____ | ____ | ____ | ____ |
| Link R1–R2 | — | 2 | ____ | ____ | n/a | ____ |
| Link R1–L3SW | — | 2 | ____ | ____ | n/a | ____ |
| Link R2–L3SW | — | 2 | ____ | ____ | n/a | ____ |

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
