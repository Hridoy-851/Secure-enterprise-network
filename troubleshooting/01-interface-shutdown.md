# Incident 01 — Interface Shutdown

## Scenario

An endpoint suddenly lost network connectivity because its switch access port was administratively shut down.

## Affected Device

- **Device:** ASW1
- **Interface:** FastEthernet0/10
- **VLAN:** VLAN 10 — ADMIN
- **Endpoint:** ADMIN-PC

## Symptoms

ADMIN-PC could not communicate with the network.

The expected connectivity to the ADMIN VLAN gateway was unavailable.

## Investigation

The first step was to check the interface status:

    show ip interface brief

The affected interface was found to be administratively down.

Additional verification was performed with:

    show interfaces FastEthernet0/10 status

This confirmed that the access port was not operational.

The VLAN assignment was also checked:

    show vlan brief

FastEthernet0/10 was configured as an access port in VLAN 10.

## Root Cause

FastEthernet0/10 had been manually placed into the shutdown state.

The problem was therefore an interface availability issue rather than a DHCP, routing, or VLAN configuration problem.

## Resolution

The interface was restored with:

    enable
    configure terminal
    interface FastEthernet0/10
    no shutdown
    end

The interface was then verified again:

    show ip interface brief

The interface returned to an operational state.

## Verification

After the interface was restored:

- Fa0/10 returned to an operational state.
- ADMIN-PC regained network connectivity.
- The ADMIN VLAN gateway became reachable.
- Normal network operation was restored.

Connectivity was verified using:

    ping 10.50.0.129

The gateway responded successfully.

## Lessons Learned

This incident demonstrates the importance of checking interface status before troubleshooting higher-layer problems.

A useful troubleshooting sequence is:

1. Check physical and interface status.
2. Check VLAN assignment.
3. Check trunk connectivity if applicable.
4. Check IP addressing and DHCP.
5. Check routing.
6. Test end-to-end connectivity.

Starting with the simplest Layer 1/Layer 2 checks can quickly identify interface-related connectivity problems.

## Skills Practiced

- Cisco IOS interface troubleshooting
- Access-port verification
- `show ip interface brief`
- `show interfaces ... status`
- `show vlan brief`
- Interface recovery using `no shutdown`
- Basic Layer 1/Layer 2 troubleshooting

## Result

**Status: Resolved**

The shutdown interface was restored and ADMIN-PC connectivity was successfully recovered.
