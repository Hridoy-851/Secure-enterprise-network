# Troubleshooting

This directory documents troubleshooting scenarios performed during the development and verification of the Secure Enterprise Network.

The scenarios were designed to practice fault isolation, verification, recovery, and documentation using Cisco Packet Tracer.

## Troubleshooting Scenarios

| # | Scenario | Main Skill |
|---|---|---|
| 01 | Interface Shutdown | Interface troubleshooting |
| 02 | Wrong VLAN Assignment | VLAN troubleshooting |
| 03 | Trunk Failure | 802.1Q trunk troubleshooting |
| 04 | DHCP Relay Failure | DHCP troubleshooting |
| 05 | Port Security Failure | Layer 2 security |
| 06 | Native VLAN Mismatch | Trunk/native VLAN troubleshooting |
| 07 | Static Route Failure | Layer 3 routing |
| 08 | Default Route Failure | Default routing |
| 09 | NAT/PAT Failure | NAT troubleshooting |
| 10 | Trunk Allowed VLAN Failure | VLAN/trunk troubleshooting |
| 11 | Server Port Security Test | Port-security verification |

## Troubleshooting Method

Each scenario follows a structured approach:

1. Identify the symptom.
2. Verify the affected device/interface.
3. Check the relevant configuration and operational state.
4. Identify the root cause.
5. Apply the corrective configuration.
6. Verify connectivity and service restoration.
7. Save the corrected configuration.

## Verification Commands

Common commands used throughout the troubleshooting process include:

```text
show ip interface brief
show interfaces trunk
show vlan brief
show ip route
show arp
show mac address-table
show port-security
show port-security interface
show ip dhcp binding
show ip nat translations
show ip nat statistics
show access-lists
show ip ssh
ping
traceroute
