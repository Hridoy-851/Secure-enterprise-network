# Incident 03 — Trunk Failure

## Scenario

Multiple VLANs lost connectivity between ASW1 and DSW1 because the trunk link between the switches was unavailable.

## Affected Devices

- **Device:** ASW1
- **Interface:** GigabitEthernet0/1
- **Connected Device:** DSW1
- **Link Type:** 802.1Q trunk

## Symptoms

Devices connected to ASW1 could not reach resources located through DSW1.

Multiple VLANs were affected rather than only one endpoint.

## Investigation

The trunk status was checked:

    show interfaces trunk

GigabitEthernet0/1 was not operating as an active trunk.

The interface status was also checked:

    show ip interface brief

The trunk interface was found to be unavailable.

## Root Cause

The ASW1-to-DSW1 trunk interface had been shut down.

Because the trunk carried multiple VLANs, several VLANs lost connectivity simultaneously.

## Resolution

The trunk interface was restored:

    enable
    configure terminal
    interface GigabitEthernet0/1
    no shutdown
    end

## Verification

The trunk was checked again:

    show interfaces trunk

GigabitEthernet0/1 was confirmed to be operational as an 802.1Q trunk.

The configured native VLAN was verified as VLAN 999.

The required VLANs were also confirmed to be allowed:

    10,20,30,40,50,60,99,999

Connectivity was then tested from affected endpoints.

## Lessons Learned

When multiple VLANs fail at the same time, checking the trunk between switches is an important troubleshooting step.

## Skills Practiced

- 802.1Q trunk troubleshooting
- `show interfaces trunk`
- VLAN transport verification
- Switch-to-switch connectivity troubleshooting
- Interface recovery

## Result

**Status: Resolved**

The ASW1-to-DSW1 trunk was restored and VLAN connectivity recovered.
