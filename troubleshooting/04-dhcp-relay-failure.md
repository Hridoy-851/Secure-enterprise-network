# Incident 04 — DHCP Relay Failure

## Scenario

Clients in VLAN 10 were unable to obtain DHCP addresses because the DHCP relay configuration on DSW1 was missing.

## Affected Device

- **Device:** DSW1
- **Interface:** VLAN 10 SVI
- **VLAN:** VLAN 10 — ADMIN
- **DHCP Server:** SRV1
- **DHCP Server IP:** 10.50.1.2

## Symptoms

ADMIN-PC was unable to obtain a valid IP address automatically.

The DHCP server was located in VLAN 50, while the client was located in VLAN 10.

## Investigation

The VLAN 10 SVI configuration was checked:

    show running-config interface Vlan10

The DHCP relay configuration was missing.

The DHCP server was reachable from DSW1, but DHCP broadcast traffic from VLAN 10 was not being forwarded to the server.

## Root Cause

The DHCP relay command was missing from the VLAN 10 SVI.

DHCP client broadcasts do not normally cross Layer 3 boundaries without a DHCP relay.

## Resolution

The DHCP relay was restored:

    enable
    configure terminal
    interface Vlan10
    ip helper-address 10.50.1.2
    end

## Verification

The SVI configuration was checked:

    show running-config interface Vlan10

The helper address was confirmed.

ADMIN-PC was then configured to obtain its address through DHCP.

The resulting address was verified as being in the ADMIN subnet with gateway 10.50.0.129.

## Lessons Learned

When a DHCP server is located in another subnet, the Layer 3 gateway must relay DHCP requests.

The `ip helper-address` command is therefore an important part of DHCP troubleshooting in routed networks.

## Skills Practiced

- DHCP troubleshooting
- DHCP relay
- `ip helper-address`
- SVI configuration
- Inter-VLAN DHCP operation

## Result

**Status: Resolved**

The DHCP relay was restored and VLAN 10 clients successfully obtained DHCP configuration from SRV1.
