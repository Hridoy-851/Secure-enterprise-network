# Incident 10 — Trunk Allowed VLAN Failure

## Scenario

IT-OPS connectivity failed because VLAN 40 was removed from the allowed VLAN list on the ASW1-to-DSW1 trunk.

## Affected Devices

- **Device:** ASW1
- **Interface:** GigabitEthernet0/1
- **Affected VLAN:** VLAN 40 — IT-OPS
- **Endpoint:** IT-OPS-PC

## Symptoms

IT-OPS-PC could not communicate through the network.

Other VLANs using the same trunk continued to operate normally.

## Investigation

The trunk configuration was checked:

    show interfaces trunk

The allowed VLAN list did not contain VLAN 40.

This indicated that the physical trunk was operational but VLAN 40 traffic was not permitted across the trunk.

## Root Cause

VLAN 40 had been removed from the trunk allowed VLAN list on ASW1 GigabitEthernet0/1.

The trunk therefore remained operational while specifically blocking VLAN 40.

## Resolution

VLAN 40 was added back to the allowed VLAN list:

    enable
    configure terminal
    interface GigabitEthernet0/1
    switchport trunk allowed vlan 10,20,30,40,50,60,99,999
    end

## Verification

The trunk was checked again:

    show interfaces trunk

VLAN 40 was confirmed to be both allowed and forwarding.

IT-OPS-PC connectivity was then tested.

The IT-OPS gateway was reachable:

    ping 10.50.0.225

## Lessons Learned

A trunk can remain operational while individual VLANs fail if those VLANs are not included in the allowed VLAN list.

When only one VLAN fails across a trunk, check:

1. VLAN existence.
2. Access-port assignment.
3. Trunk allowed VLAN list.
4. Native VLAN configuration.
5. End-to-end connectivity.

## Skills Practiced

- Trunk troubleshooting
- Allowed VLAN configuration
- `show interfaces trunk`
- VLAN-specific connectivity troubleshooting
- 802.1Q trunk verification

## Result

**Status: Resolved**

VLAN 40 was restored to the trunk allowed list and IT-OPS connectivity was recovered.
