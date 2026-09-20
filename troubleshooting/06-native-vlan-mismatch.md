# Incident 06 — Native VLAN Mismatch

## Scenario

The trunk between ASW1 and DSW1 had a native VLAN mismatch.

## Affected Devices

- **Device:** ASW1
- **Interface:** GigabitEthernet0/1
- **Device:** DSW1
- **Interface:** GigabitEthernet0/1
- **Expected Native VLAN:** VLAN 999

## Symptoms

A native VLAN mismatch warning was observed on the switch trunk.

The switches were using different native VLAN values.

## Investigation

The trunk configuration was checked:

    show interfaces trunk

The native VLAN configuration on both switches was compared.

One side was using VLAN 999 while the other side was using a different native VLAN.

## Root Cause

The two ends of the 802.1Q trunk were configured with different native VLANs.

This creates inconsistent handling of untagged traffic across the trunk.

## Resolution

The native VLAN was aligned on both trunk interfaces.

On ASW1:

    enable
    configure terminal
    interface GigabitEthernet0/1
    switchport trunk native vlan 999
    end

On DSW1:

    enable
    configure terminal
    interface GigabitEthernet0/1
    switchport trunk native vlan 999
    end

## Verification

The trunk configuration was checked on both switches:

    show interfaces trunk

Both sides were confirmed to use:

    Native VLAN: 999

The required VLANs were also confirmed to be allowed across the trunk.

## Lessons Learned

The native VLAN must match on both ends of an 802.1Q trunk.

A mismatch can produce warnings and inconsistent Layer 2 behavior.

## Skills Practiced

- 802.1Q trunk configuration
- Native VLAN troubleshooting
- `show interfaces trunk`
- Switch-to-switch Layer 2 troubleshooting

## Result

**Status: Resolved**

The native VLAN was aligned to VLAN 999 on both switches.
