# Troubleshooting Case 1: Clients Receiving APIPA

## Symptom

Clients in several VLANs received "169.254.0.0/16" APIPA addresses instead of DHCP leases.

## Initial checks

- Router physical interfaces and subinterfaces were up/up.
- Subinterface VLAN tags matched the intended VLAN IDs.
- DHCP pools used the correct `/29` masks.
- Gateway addresses were excluded.
- `show ip dhcp binding` contained no new leases.

## Evidence

"show vlan brief" showed that each switch contained only its locally assigned VLANs. "show interfaces trunk" therefore listed only those VLANs as allowed and active, even though the trunk's allowed range was broader.

## Root cause

Required VLANs were missing from the VLAN databases of switches that needed to carry those VLANs in transit. DHCP broadcasts were dropped before reaching the router subinterfaces.

## Resolution

Create the required VLANs on every switch in the Layer 2 path and verify trunk forwarding. Example:

```cisco
vlan 2
 name Sales
vlan 3
 name IT
vlan 4
 name Marketing
vlan 10
 name HR
vlan 15
 name Management
vlan 20
 name HelpDesk
vlan 21
 name Voice
```

## Verification

```cisco
show vlan brief
show interfaces trunk
show ip dhcp binding
```

After renewing the clients, they received addresses from their correct "/29" pools.
