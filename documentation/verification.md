# Final Verification Report

## Project 3 — Secure Enterprise Network

This document records the final verification performed after configuration and troubleshooting were completed.

## 1. Layer 2 Verification

### DSW1

Verified:

- VLANs 10, 20, 30, 40, 50, 60, 99, and 999 exist.
- SRV1 is connected to `Fa0/1` in VLAN 50.
- Unused access ports are administratively down.
- `Gi0/1` is an 802.1Q trunk.
- Native VLAN is `999`.
- Required VLANs `10,20,30,40,50,60,99,999` are allowed and forwarding.

### ASW1

Verified:

- ADMIN `Fa0/10` → VLAN 10
- HR `Fa0/12` → VLAN 20
- FINANCE `Fa0/14` → VLAN 30
- IT-OPS `Fa0/16` → VLAN 40
- GUEST `Fa0/24` → VLAN 60
- `Gi0/1` is an 802.1Q trunk.
- Native VLAN is `999`.
- Required VLANs `10,20,30,40,50,60,99,999` are allowed and forwarding.
- VLAN 99 management SVI is `10.50.0.242`.
- Unused ports are administratively down.

## 2. Layer 3 Verification

DSW1 final SVIs:

| SVI | Address | Status |
|---|---|---|
| Vlan10 | `10.50.0.129/27` | Up/Up |
| Vlan20 | `10.50.0.193/27` | Up/Up |
| Vlan30 | `10.50.0.161/27` | Up/Up |
| Vlan40 | `10.50.0.225/28` | Up/Up |
| Vlan50 | `10.50.1.1/28` | Up/Up |
| Vlan60 | `10.50.0.1/25` | Up/Up |
| Vlan99 | `10.50.0.241/28` | Up/Up |

Routed links:

| Link | Result |
|---|---|
| DSW1 Gi0/2 `10.50.1.249` ↔ R1 Gi0/0 `10.50.1.250` | Up/Up |
| R1 Gi0/1 `10.50.1.253` ↔ ISP Gi0/0 `10.50.1.254` | Up/Up |

## 3. DHCP Verification

The following client VLANs were individually tested and successfully received DHCP configuration:

- VLAN 10 — ADMIN
- VLAN 20 — HR
- VLAN 30 — FINANCE
- VLAN 40 — IT-OPS
- VLAN 60 — GUEST

Final DSW1 configuration contains DHCP relay:

```text
ip helper-address 10.50.1.2
```

on each of the five client SVIs.

## 4. Inter-VLAN Connectivity

Final endpoint tests were successful from:

- ADMIN-PC
- HR-PC
- FINANCE-PC
- IT-OPS-PC
- GUEST-PC

Tests included connectivity to:

- Local gateway
- Other VLAN gateways
- Server VLAN gateway
- SRV1
- R1 inside interface
- R1 WAN interface
- ISP interface

The results confirmed operational inter-VLAN routing and end-to-end forwarding.

## 5. Server Verification

SRV1 final configuration:

```text
IPv4 Address: 10.50.1.2
Subnet Mask: 255.255.255.240
Default Gateway: 10.50.1.1
```

SRV1 successfully reached:

- DSW1 VLAN 50 gateway
- ADMIN gateway
- ADMIN-PC
- R1

## 6. SSH Verification

ASW1, DSW1, and R1 reported:

```text
SSH Enabled - version 2.0
```

Switch VTY lines use:

```text
login local
transport input ssh
```

ASW1 and DSW1 also use the `MANAGEMENT-SSH` ACL.

Final management verification confirmed ADMIN-based SSH access and rejection of unauthorized Guest SSH access to the switches.

## 7. NAT/PAT Verification

R1 final NAT roles:

```text
Inside  = GigabitEthernet0/0
Outside = GigabitEthernet0/1
```

The enterprise source ACL permits:

```text
10.50.0.0/23
```

A fresh NAT test generated active translations including:

```text
Inside local:  10.50.0.2
Inside global: 10.50.1.253
Outside:       8.8.8.8
```

Final statistics showed active dynamic translations and NAT hits.

This verifies PAT operation.

## 8. WAN Verification

R1 successfully pinged:

```text
10.50.1.249 → DSW1
10.50.1.254 → ISP
```

Both tests achieved:

```text
Success rate is 100 percent
```

R1 default route:

```text
0.0.0.0/0 → 10.50.1.254
```

ISP return route:

```text
10.50.0.0/23 → 10.50.1.253
```

## 9. Security Verification

Final configuration was checked for:

- Port security
- Sticky MAC
- DHCP Snooping
- Dynamic ARP Inspection
- PortFast
- BPDU Guard
- Unused-port shutdown
- Native VLAN 999
- SSH v2
- Management ACL
- Guest isolation ACL
- NAT source ACL

All intended controls were present in the final configuration.

## 10. Troubleshooting Verification

Approximately 30 intentional troubleshooting scenarios were completed across:

- Access ports
- VLAN assignment
- Trunking
- Native VLAN
- DHCP relay
- DHCP Snooping
- DAI
- Port Security
- SSH
- Static routing
- Default routing
- Routed interfaces
- WAN connectivity
- NAT/PAT
- DHCP pool configuration

The final configuration was restored after each successful fault-isolation exercise.

### Scenario #29 — Trunk Allowed-VLAN Failure

VLAN 40 was intentionally removed from the ASW1–DSW1 trunk.

Result:

- IT-OPS connectivity failed.
- VLAN 40 was restored to the trunk.
- IT-OPS connectivity returned successfully.

### Scenario #30 — Server Port-Security Violation

A second endpoint was used to attempt a server-port MAC violation.

Packet Tracer retained a single sticky MAC and reported:

```text
Security Violation Count: 0
Port Status: Secure-up
```

The violation was therefore not counted as successfully reproduced.

This behavior was documented rather than misrepresented.

## 11. Guest Isolation Limitation

The Guest isolation ACL is installed on R1, but DSW1 performs local inter-VLAN routing.

Therefore, Guest-to-internal traffic can bypass R1 when routed locally by DSW1.

The final tests demonstrated this behavior.

This is documented as a topology/design limitation rather than a successful complete isolation implementation.

## 12. Overall Result

The final project successfully demonstrates:

- VLSM addressing
- VLAN segmentation
- 802.1Q trunking
- Multilayer switching
- Inter-VLAN routing
- DHCP relay
- Centralized DHCP
- Static routing
- Default routing
- NAT/PAT
- SSH management
- Access-layer security
- Troubleshooting methodology
- Verification and documentation

The final `.pkt` file was saved after verification.
