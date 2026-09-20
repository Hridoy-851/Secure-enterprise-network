# Incident 02 — Wrong VLAN Assignment

## Scenario

An endpoint lost network connectivity because its switch access port was assigned to the wrong VLAN.

## Affected Device

- **Device:** ASW1
- **Interface:** FastEthernet0/10
- **Expected VLAN:** VLAN 10 — ADMIN
- **Endpoint:** ADMIN-PC

## Symptoms

ADMIN-PC could not communicate correctly with the ADMIN VLAN gateway.

The endpoint was connected physically, but normal network connectivity was unavailable.

## Investigation

The VLAN assignment was checked:

    show vlan brief

FastEthernet0/10 was found in the wrong VLAN.

The interface configuration was also checked:

    show running-config interface FastEthernet0/10

This confirmed that the access port was not assigned to the expected ADMIN VLAN.

## Root Cause

FastEthernet0/10 had been assigned to the wrong VLAN.

Because the endpoint was placed into the wrong Layer 2 broadcast domain, it could not communicate normally with the ADMIN VLAN gateway.

## Resolution

The correct VLAN was restored:

    enable
    configure terminal
    interface FastEthernet0/10
    switchport mode access
    switchport access vlan 10
    end

## Verification

The VLAN assignment was verified:

    show vlan brief

FastEthernet0/10 was confirmed to be in VLAN 10.

Connectivity to the ADMIN VLAN gateway was then tested:

    ping 10.50.0.129

The gateway responded successfully.

## Lessons Learned

An incorrect access VLAN can cause connectivity problems even when the physical interface is operational.

A useful troubleshooting sequence is:

1. Check interface status.
2. Check VLAN assignment.
3. Check IP addressing.
4. Check the default gateway.
5. Test connectivity.

## Skills Practiced

- VLAN troubleshooting
- Access-port configuration
- `show vlan brief`
- `show running-config interface`
- Cisco IOS Layer 2 troubleshooting

## Result

**Status: Resolved**

FastEthernet0/10 was restored to VLAN 10 and ADMIN-PC connectivity was successfully recovered.
