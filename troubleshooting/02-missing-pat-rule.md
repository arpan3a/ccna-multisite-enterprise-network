# Troubleshooting Case 2: PAT Translations Missing

## Symptom

Toronto clients could reach their default gateways and R1's outside interface, but they could not reach the ISP test destination. "show ip nat translations" was empty.

## Working components

- Client IPv4 address, mask, and gateway were correct.
- LAN subinterfaces had "ip nat inside".
- The ISP-facing interface had "ip nat outside".
- ACL 1 correctly matched "192.168.1.0–192.168.1.63".
- R1 had a default route through Serial0/1.
- R1 itself could reach the ISP.

## Root cause

The translation rule connecting the NAT ACL to the outside interface was missing.

## Resolution

```cisco
ip nat inside source list 1 interface Serial0/1 overload
```

## Verification

Generate inside-to-outside traffic, then check:

```cisco
show ip nat translations
show ip nat statistics
show access-lists
```

PAT entries then showed inside-local client addresses translated to R1's ISP-facing inside-global address.

## Technical note

Without PAT, a router may still forward a private-source packet, but the simulated ISP has no return route to the private client network. PAT supplies a source address reachable on the ISP-facing network and enables the return path.
