# Incident 09 — NAT/PAT Failure

## Scenario

Enterprise traffic reached R1 but was not being translated correctly because the NAT/PAT configuration was unavailable.

## Affected Device

- **Device:** R1
- **Inside Interface:** GigabitEthernet0/0
- **Outside Interface:** GigabitEthernet0/1
- **NAT Type:** PAT

## Symptoms

Traffic from enterprise networks was not creating the expected NAT translations.

## Investigation

The NAT configuration was checked:

    show running-config | include ip nat

The NAT translations were also checked:

    show ip nat translations

No expected dynamic translation was present for the test traffic.

NAT statistics were checked:

    show ip nat statistics

## Root Cause

The NAT/PAT rule was unavailable or incorrectly configured.

The enterprise networks were therefore not being translated to the R1 WAN interface address.

## Resolution

The NAT ACL was verified:

    access-list 1 permit 10.50.0.0 0.0.1.255

PAT was restored with:

    ip nat inside source list 1 interface GigabitEthernet0/1 overload

The interface roles were also verified:

    interface GigabitEthernet0/0
    ip nat inside

    interface GigabitEthernet0/1
    ip nat outside

## Verification

Enterprise traffic was generated again.

The translation table was checked:

    show ip nat translations

A translation similar to the following was observed:

    Inside local: 10.50.0.2
    Inside global: 10.50.1.253

NAT statistics also showed translation activity.

## Packet Tracer Note

The WAN in this project is a simulated ISP environment.

NAT translation activity demonstrates that PAT is functioning within the lab, but it does not prove access to the real Internet.

## Lessons Learned

NAT troubleshooting requires checking three major areas:

1. NAT ACL.
2. Inside/outside interface roles.
3. NAT translation table and statistics.

## Skills Practiced

- NAT/PAT
- Standard ACLs
- `show ip nat translations`
- `show ip nat statistics`
- NAT inside/outside interface configuration

## Result

**Status: Resolved**

PAT was restored and enterprise traffic successfully generated NAT translations.
