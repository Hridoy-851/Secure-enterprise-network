# Incident 11 — Server Port Security Test

## Scenario

A port-security test was performed on the server access port to verify the configured security policy.

## Affected Device

- **Device:** DSW1
- **Interface:** FastEthernet0/1
- **VLAN:** VLAN 50 — SERVERS
- **Endpoint:** SRV1
- **Maximum Secure MAC Addresses:** 1
- **Violation Mode:** Shutdown

## Objective

The purpose of this test was to verify that the server access port was protected by port security and to observe how Packet Tracer handled an additional device connected to the protected interface.

## Configuration

The server port was configured with port security allowing one secure MAC address.

The port also used sticky MAC learning.

Verification was performed with:

    show port-security interface FastEthernet0/1

The final configuration showed:

- Port Security: Enabled
- Port Status: Secure-up
- Violation Mode: Shutdown
- Maximum MAC Addresses: 1
- Sticky MAC Addresses: 1
- Security Violation Count: 0

The learned secure MAC address was also verified:

    show port-security address

The server MAC address was listed as a secure sticky address on Fa0/1.

## Test Procedure

An additional PC was temporarily connected to the protected server port to test whether Packet Tracer would trigger a second-MAC violation.

The port-security status was then checked again.

## Result

Packet Tracer did not reproduce a second-MAC security violation during this test.

The port remained operational and the security violation counter remained at zero.

The original SRV1 was then reconnected to Fa0/1.

## Verification

The port-security status was checked:

    show port-security interface FastEthernet0/1

The final state was:

- Port Security: Enabled
- Port Status: Secure-up
- Maximum MAC Addresses: 1
- Sticky MAC Addresses: 1
- Security Violation Count: 0

SRV1 connectivity was also verified after reconnecting the server.

## Packet Tracer Limitation

This test should be interpreted as a Packet Tracer behavior observation rather than proof that the same event would occur identically on physical Cisco hardware.

The expected security policy was configured, but the additional-device test did not trigger the expected violation in this simulation.

The result is documented honestly instead of claiming that a violation occurred when it did not.

## Lessons Learned

Port-security configuration and actual violation behavior should both be verified.

Useful verification commands include:

    show port-security interface FastEthernet0/1

    show port-security address

A lab simulator may not reproduce every hardware behavior exactly.

## Skills Practiced

- Server access-port security
- Port security
- Sticky MAC addresses
- Port-security verification
- Cisco IOS security troubleshooting
- Packet Tracer behavior analysis

## Result

**Status: Test Completed**

Port security was confirmed to be enabled and the server port remained secure-up.

The additional-device test did not trigger a violation in Packet Tracer, so no violation was claimed.
