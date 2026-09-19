# DHCP and Routing

## Project 3 — Secure Enterprise Network

This document describes the DHCP, inter-VLAN routing, WAN routing, and NAT/PAT design used in the final implementation.

## 1. DHCP Architecture

DHCP services are hosted on SRV1:

```text
SRV1 = 10.50.1.2/28
Gateway = 10.50.1.1
```

The DSW1 multilayer switch acts as the default gateway for the client VLANs and relays DHCP requests to SRV1.

### DHCP relay interfaces

The following DSW1 SVIs use:

```cisco
ip helper-address 10.50.1.2
```

- VLAN 10 — ADMIN
- VLAN 20 — HR
- VLAN 30 — FINANCE
- VLAN 40 — IT-OPS
- VLAN 60 — GUEST

VLAN 50 does not require a relay because SRV1 is directly connected to VLAN 50.

## 2. DHCP Pools

| VLAN | Pool Start | Prefix | Gateway | DNS |
|---:|---|---|---|---|
| 10 ADMIN | `10.50.0.131` | `/27` | `10.50.0.129` | `10.50.1.2` |
| 20 HR | `10.50.0.194` | `/27` | `10.50.0.193` | `10.50.1.2` |
| 30 FINANCE | `10.50.0.162` | `/27` | `10.50.0.161` | `10.50.1.2` |
| 40 IT-OPS | `10.50.0.226` | `/28` | `10.50.0.225` | `10.50.1.2` |
| 60 GUEST | `10.50.0.2` | `/25` | `10.50.0.1` | `10.50.1.2` |

All five client VLAN DHCP pools were individually tested during the project.

## 3. Inter-VLAN Routing

DSW1 is a multilayer switch with:

```cisco
ip routing
```

The SVI gateways are:

| VLAN | SVI | Address |
|---:|---|---|
| 10 | Vlan10 | `10.50.0.129/27` |
| 20 | Vlan20 | `10.50.0.193/27` |
| 30 | Vlan30 | `10.50.0.161/27` |
| 40 | Vlan40 | `10.50.0.225/28` |
| 50 | Vlan50 | `10.50.1.1/28` |
| 60 | Vlan60 | `10.50.0.1/25` |
| 99 | Vlan99 | `10.50.0.241/28` |

All required SVIs were verified `up/up` in the final state.

## 4. DSW1 ↔ R1 Routed Link

The Layer-3 transit network is:

```text
10.50.1.248/30
```

| Device | Interface | Address |
|---|---|---|
| DSW1 | Gi0/2 | `10.50.1.249/30` |
| R1 | Gi0/0 | `10.50.1.250/30` |

DSW1 uses R1 as its default route:

```cisco
ip route 0.0.0.0 0.0.0.0 10.50.1.250
```

## 5. R1 Enterprise Routes

R1 uses static routes to reach the VLAN and server networks through DSW1:

```cisco
ip route 10.50.0.0 255.255.255.128 10.50.1.249
ip route 10.50.0.128 255.255.255.224 10.50.1.249
ip route 10.50.0.160 255.255.255.224 10.50.1.249
ip route 10.50.0.192 255.255.255.224 10.50.1.249
ip route 10.50.0.224 255.255.255.240 10.50.1.249
ip route 10.50.1.0 255.255.255.240 10.50.1.249
ip route 10.50.0.240 255.255.255.240 10.50.1.249
```

These routes allow return traffic from R1 toward the internal networks.

## 6. R1 ↔ ISP Link

The WAN transit network is:

```text
10.50.1.252/30
```

| Device | Interface | Address |
|---|---|---|
| R1 | Gi0/1 | `10.50.1.253/30` |
| ISP | Gi0/0 | `10.50.1.254/30` |

R1 uses:

```cisco
ip route 0.0.0.0 0.0.0.0 10.50.1.254
```

Final verification showed R1 could ping both:

```text
10.50.1.249  → DSW1
10.50.1.254  → ISP
```

with 100% success.

## 7. ISP Return Route

The simulated ISP has a static route for the entire enterprise `/23`:

```cisco
ip route 10.50.0.0 255.255.254.0 10.50.1.253
```

This provides the return path toward the enterprise network.

The ISP routing table was verified in the final state.

## 8. NAT/PAT

R1 performs PAT for the enterprise address block.

### NAT roles

```text
Inside  = GigabitEthernet0/0
Outside = GigabitEthernet0/1
```

The NAT source ACL permits the enterprise `/23`:

```cisco
access-list 1 permit 10.50.0.0 0.0.1.255
```

PAT is configured using the R1 WAN interface:

```cisco
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

This translates internal addresses to R1's WAN address:

```text
Inside local  → 10.50.0.x
Inside global → 10.50.1.253
```

## 9. NAT Verification

A final NAT test generated active translations such as:

```text
Inside local:  10.50.0.2
Inside global: 10.50.1.253
Outside:       8.8.8.8
```

Final NAT statistics showed dynamic translations and increasing hit counts.

The simulated ISP itself does not provide a real upstream Internet connection. Therefore, an ICMP test to `8.8.8.8` should not be interpreted as proof of real Internet connectivity. The NAT translation itself was verified successfully.

## 10. Routing Troubleshooting Coverage

Routing-related scenarios included:

- DSW1 routed-interface failure
- R1 WAN-link failure
- R1 default-route failure
- ISP return-route failure
- NAT/PAT rule failure
- NAT inside/outside role failure
- R1 static-route failure
- R1 static-route next-hop failure
- DSW1 default-route failure
- DSW1 ↔ R1 IP addressing failure

Each intentionally introduced fault was restored to the final working configuration.

## 11. Final Routing Verification

The final implementation confirmed:

- All client VLAN gateways operational
- Inter-VLAN routing operational
- SRV1 reachable from client VLANs
- DSW1 ↔ R1 routed link operational
- R1 ↔ ISP link operational
- R1 enterprise static routes installed
- DSW1 default route installed
- R1 default route installed
- ISP return route installed
- NAT/PAT operational
- NAT translations observable on R1

## 12. Packet Tracer Scope

This project uses an ISP router as a simulated upstream network. It is not a connection to the public Internet.

The routing and NAT configuration demonstrates the control-plane and forwarding concepts expected in an enterprise edge design, while the actual Internet service provider and upstream Internet are intentionally simulated.
