# Troubleshooting Runbook

> Ticket-style log. Each time something breaks (and it will), record it here.
> This is the single most CV-relevant doc in the repo — it shows you can *support*, not just build.

## Template — copy this block per ticket
```
### TICKET-00X · <short title>
- Reported: <what the "user" saw>
- VLAN / device: <where>
- Diagnosis: <how you found the cause>
- Fix: <what you changed>
- Root cause: <why it happened>
```

---

### TICKET-001 · Client gets no IP address
- Reported: ____________________________________________
- VLAN / device: ____________________
- Diagnosis: <!-- e.g. checked helper-address on the SVI, checked scope --> ____
- Fix: ____________________________________________
- Root cause: ____________________________________________

### TICKET-002 · Can't reach another VLAN
- Reported: ____________________________________________
- VLAN / device: ____________________
- Diagnosis: ____________________________________________
- Fix: ____________________________________________
- Root cause: ____________________________________________

### TICKET-003 · Domain join fails
- Reported: ____________________________________________
- VLAN / device: ____________________
- Diagnosis: ____________________________________________
- Fix: ____________________________________________
- Root cause: ____________________________________________

<!-- ✍️ Add more as they come up: OSPF neighbour stuck, EtherChannel won't bundle,
     trunk native-VLAN mismatch, DNS not resolving, etc. -->
