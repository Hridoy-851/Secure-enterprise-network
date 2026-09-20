# Incident 05 — Port Security Failure

## Scenario

A switch access port became unavailable after a port-security violation.

## Affected Device

- **Device:** ASW1
- **Interface:** FastEthernet0/24
- **VLAN:** VLAN 60 — GUEST
- **Security:** Port Security
- **Violation Mode:** Shutdown

## Symptoms

The endpoint connected to Fa0/24 lost network connectivity.

The interface was no longer forwarding traffic.

## Investigation

Port-security status was checked:

    show port-security interface FastEthernet0/24

The interface was found to be in a security violation state.

The configured maximum number of secure MAC addresses was one.

## Root Cause

The port-security policy detected an unauthorized MAC address on the protected access port.

The configured violation mode was shutdown, causing the interface to become unavailable.

## Resolution

The interface was recovered after the violation was cleared:

    enable
    configure terminal
    interface FastEthernet0/24
    shutdown
    no shutdown
    end

The port-security state was then checked again:

    show port-security interface FastEthernet0/24

## Verification

The interface returned to a secure operational state.

The endpoint regained network connectivity.

The port remained protected by the configured port-security policy.

## Lessons Learned

Port security can automatically protect an access port when an unexpected MAC address is detected.

When a protected port becomes unavailable, `show port-security interface` is an important troubleshooting command.

## Skills Practiced

- Port security
- Secure MAC address verification
- Violation troubleshooting
- Interface recovery
- Layer 2 security

## Result

**Status: Resolved**

The port-security violation was cleared and the protected access port was restored.
