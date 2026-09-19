# Security Configuration

## Project 3 — Secure Enterprise Network

This document summarizes the security controls implemented and verified in the final Cisco Packet Tracer network.

## 1. Port Security

Port security was implemented on protected access ports.

- Maximum MAC addresses: `1`
- Sticky MAC learning enabled
- Violation mode: `shutdown`
- PortFast enabled on endpoint ports
- BPDU Guard enabled on endpoint ports

| Device | Port | VLAN | Endpoint |
|---|---|---:|---|
| ASW1 | Fa0/10 | 10 | ADMIN-PC |
| ASW1 | Fa0/12 | 20 | HR-PC |
| ASW1 | Fa0/14 | 30 | FINANCE-PC |
| ASW1 | Fa0/16 | 40 | IT-OPS-PC |
| ASW1 | Fa0/24 | 60 | GUEST-PC |
| DSW1 | Fa0/1 | 50 | SRV1 |

Port-security recovery was tested during troubleshooting. A server-port violation was also attempted; Packet Tracer did not produce a second-MAC violation, so the result was documented honestly.

## 2. DHCP Snooping

DHCP Snooping was enabled for VLANs 10, 20, 30, 40, and 60.

ASW1 `Gi0/1` toward DSW1 is trusted.

This protects the access layer against unauthorized DHCP server responses. A DHCP Snooping trust failure was intentionally tested and restored.

## 3. Dynamic ARP Inspection

DAI was configured for VLANs 10, 20, 30, 40, and 60.

ASW1 `Gi0/1` is trusted.

DAI uses DHCP Snooping information to validate ARP behavior. A DAI trust scenario was tested and restored.

> Packet Tracer has simulator-specific limitations for DAI, so verification was performed according to supported behavior.

## 4. STP Edge Protection

Endpoint ports use:

```text
spanning-tree portfast
spanning-tree bpduguard enable
```

This provides rapid edge-port convergence and protection against unexpected BPDUs.

## 5. Unused-Port Hardening

Unused switch ports were administratively shut down on ASW1 and DSW1, reducing the available physical attack surface.

## 6. Native VLAN Hardening

VLAN `999`, named `NATIVE-DEAD`, is used as the native VLAN on the ASW1–DSW1 trunk.

The trunk allows:

```text
10,20,30,40,50,60,99,999
```

A native-VLAN mismatch was intentionally introduced during troubleshooting and then corrected.

## 7. SSH Management

ASW1, DSW1, and R1 use SSH version 2.

Management configuration includes:

- SSH v2
- Local authentication
- RSA keys
- SSH-only VTY transport on the switches
- Local login authentication

The lab username is `admin`. Credentials and private keys are intentionally excluded from this repository.

## 8. Management ACL

ASW1 and DSW1 use:

```cisco
ip access-list standard MANAGEMENT-SSH
 permit 10.50.0.128 0.0.0.31
```

The ACL is applied inbound to the VTY lines.

Final verification confirmed ADMIN-PC SSH access and rejection of Guest SSH access to the switches.

## 9. Guest Isolation ACL

R1 contains:

```cisco
ip access-list extended GUEST-ISOLATION
 deny ip 10.50.0.0 0.0.0.127 10.50.0.0 0.0.0.127
 deny ip 10.50.0.0 0.0.0.127 10.50.0.128 0.0.0.31
 deny ip 10.50.0.0 0.0.0.127 10.50.0.160 0.0.0.31
 deny ip 10.50.0.0 0.0.0.127 10.50.0.192 0.0.0.31
 deny ip 10.50.0.0 0.0.0.127 10.50.0.224 0.0.0.15
 deny ip 10.50.0.0 0.0.0.127 10.50.0.240 0.0.0.15
 deny ip 10.50.0.0 0.0.0.127 10.50.1.0 0.0.0.15
 permit ip any any
```

It is applied inbound on R1 `Gi0/0`.

### Important limitation

DSW1 performs Layer-3 inter-VLAN routing. Traffic between VLANs can therefore be routed locally on DSW1 without crossing R1.

Consequently, the R1 ACL does **not** provide complete Guest-to-internal isolation in this topology.

This limitation was observed and documented rather than hidden. A production design would enforce Guest isolation at the actual Layer-3 security boundary, such as a firewall, or use an appropriate switch ACL/VACL design.

## 10. Security Troubleshooting Coverage

Security scenarios included:

- Access-port shutdown and recovery
- Wrong access VLAN
- Port-security recovery
- DHCP Snooping trust failure
- DAI trust failure
- SSH access-control failure
- Sticky MAC recovery
- Native VLAN mismatch
- Trunk allowed-VLAN failure
- Unused-port/security verification

### Server Port-Security Test

The DSW1 `Fa0/1` server port was tested with:

```text
Port Security: Enabled
Maximum MAC Addresses: 1
Sticky MAC Addresses: 1
Security Violation Count: 0
Port Status: Secure-up
```

Packet Tracer did not reproduce a second-MAC violation during this test. It was recorded as simulator behavior instead of being presented as a successful violation.

## 11. Final Security Verification

Final verification confirmed:

- Protected access ports operational
- Unused ports administratively down
- Port security enabled
- DHCP Snooping restored
- DAI restored
- BPDU Guard and PortFast configured
- Native VLAN 999 aligned
- SSH v2 operational
- Management ACL operational on switches
- Guest isolation ACL present on R1
- Simulator/design limitations documented

## 12. Credentials

Passwords, enable secrets, and private keys are not included in the repository.

For a real deployment, credentials should be unique, securely stored, and never committed to a public repository.
