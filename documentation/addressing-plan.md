# Addressing Plan

## Project 3 --- Secure Enterprise Network

This document defines the VLAN, VLSM, gateway, endpoint, server,
management, and WAN addressing used in the final Cisco Packet Tracer
implementation.

## 1. Base Network

  Purpose                 Network
  ----------------------- ---------------------------
  Enterprise VLSM block   `10.50.0.0/23`
  Address range           `10.50.0.0 – 10.50.1.255`
  Subnet mask             `255.255.254.0`

The `/23` parent block provides 512 total addresses and is divided into
smaller VLSM subnets according to department and infrastructure
requirements.

## 2. VLAN and VLSM Plan

  --------------------------------------------------------------------------------------------------
            VLAN Name          Subnet             Mask                Gateway           Usable Hosts
  -------------- ------------- ------------------ ------------------- --------------- --------------
              60 GUEST         `10.50.0.0/25`     `255.255.255.128`   `10.50.0.1`                126

              10 ADMIN         `10.50.0.128/27`   `255.255.255.224`   `10.50.0.129`               30

              30 FINANCE       `10.50.0.160/27`   `255.255.255.224`   `10.50.0.161`               30

              20 HR            `10.50.0.192/27`   `255.255.255.224`   `10.50.0.193`               30

              40 IT-OPS        `10.50.0.224/28`   `255.255.255.240`   `10.50.0.225`               14

              99 MANAGEMENT    `10.50.0.240/28`   `255.255.255.240`   `10.50.0.241`               14

              50 SERVERS       `10.50.1.0/28`     `255.255.255.240`   `10.50.1.1`                 14

             999 NATIVE-DEAD   No user subnet     ---                 ---                        ---
  --------------------------------------------------------------------------------------------------

### VLSM rationale

-   **GUEST `/25`** provides 126 usable addresses for the largest client
    population in the lab.
-   **ADMIN, FINANCE, and HR `/27`** provide 30 usable addresses each.
-   **IT-OPS `/28`** provides 14 usable addresses, appropriate for the
    smaller operations group.
-   **MANAGEMENT `/28`** provides a dedicated subnet for network-device
    management.
-   **SERVERS `/28`** provides a dedicated server segment.
-   **VLAN 999** is used as the unused native VLAN and does not carry
    normal endpoint traffic.

## 3. Endpoint Addressing

  Endpoint      VLAN Address         Mask    Default Gateway
  ----------- ------ --------------- ------- -----------------
  ADMIN-PC        10 `10.50.0.132`   `/27`   `10.50.0.129`
  HR-PC           20 `10.50.0.194`   `/27`   `10.50.0.193`
  FIN-PC          30 `10.50.0.162`   `/27`   `10.50.0.161`
  IT-OPS-PC       40 `10.50.0.226`   `/28`   `10.50.0.225`
  GUEST-PC        60 `10.50.0.2`     `/25`   `10.50.0.1`
  SRV1            50 `10.50.1.2`     `/28`   `10.50.1.1`

The five client VLANs obtain their addresses from DHCP services running
on SRV1. SRV1 itself uses a static address.

## 4. Management Addresses

  Device   Management Interface   Address         Mask    Gateway
  -------- ---------------------- --------------- ------- ---------------
  DSW1     VLAN 99 SVI            `10.50.0.241`   `/28`   ---
  ASW1     VLAN 99 SVI            `10.50.0.242`   `/28`   `10.50.0.241`

Management access is restricted through the `MANAGEMENT-SSH` standard
ACL to the ADMIN subnet `10.50.0.128/27`.

## 5. Routed WAN Links

### DSW1 ↔ R1

  Device   Interface   Address
  -------- ----------- ------------------
  DSW1     Gi0/2       `10.50.1.249/30`
  R1       Gi0/0       `10.50.1.250/30`

Network: `10.50.1.248/30`

### R1 ↔ ISP

  Device   Interface   Address
  -------- ----------- ------------------
  R1       Gi0/1       `10.50.1.253/30`
  ISP      Gi0/0       `10.50.1.254/30`

Network: `10.50.1.252/30`

## 6. Key Routing Addresses

-   DSW1 default next hop: `10.50.1.250`
-   R1 enterprise next hop: `10.50.1.249`
-   R1 default next hop: `10.50.1.254`
-   ISP return route to enterprise `/23`: via `10.50.1.253`

## 7. DHCP Design

DHCP is hosted on **SRV1 (`10.50.1.2`)**.

DHCP relay is configured on the DSW1 SVIs for:

-   VLAN 10 --- ADMIN
-   VLAN 20 --- HR
-   VLAN 30 --- FINANCE
-   VLAN 40 --- IT-OPS
-   VLAN 60 --- GUEST

Each relay uses:

``` text
10.50.1.2
```

VLAN 50 does not require a DHCP relay because SRV1 resides directly in
that subnet.

## 8. NAT Addressing

R1 performs PAT for the enterprise `10.50.0.0/23` block.

-   Inside interface: `Gi0/0` --- `10.50.1.250`
-   Outside interface: `Gi0/1` --- `10.50.1.253`
-   NAT source ACL: `10.50.0.0/23`
-   Inside-global address: R1 WAN address `10.50.1.253`

A final verification produced active ICMP translations from Guest-PC
`10.50.0.2` to the R1 WAN address `10.50.1.253`.

## 9. Addressing Verification

The final implementation was verified with:

-   All VLAN SVIs up/up on DSW1
-   DSW1--R1 routed link up/up
-   R1--ISP WAN link up/up
-   DHCP relay configured on all five client VLANs
-   Inter-VLAN connectivity tested from ADMIN, HR, FINANCE, and IT-OPS
    endpoints
-   Guest connectivity tested
-   SRV1 connectivity tested
-   NAT/PAT translations verified on R1

> **Note:** The Guest isolation ACL is applied on R1. Because DSW1
> performs local inter-VLAN routing, traffic between VLANs can be routed
> directly by DSW1 without crossing R1. Complete Guest isolation
> therefore requires an enforcement mechanism at the Layer-3
> gateway/firewall or an appropriate switch ACL/VACL design. This
> limitation was observed and documented rather than hidden.
