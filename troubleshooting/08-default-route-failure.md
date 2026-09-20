# Incident 08 — Default Route Failure

## Scenario

Enterprise traffic could not be forwarded toward the WAN because the default route on DSW1 was unavailable.

## Affected Device

- **Device:** DSW1
- **Default Gateway:** R1
- **Next Hop:** 10.50.1.250

## Symptoms

Internal VLANs could communicate with each other, but traffic destined outside the directly connected enterprise networks could not be forwarded correctly.

## Investigation

The routing table was checked:

    show ip route

The default route was missing.

Connectivity to R1 was then tested:

    ping 10.50.1.250

The R1 next hop responded successfully.

## Root Cause

The default route on DSW1 pointing toward R1 had been removed.

Without a default route, DSW1 had no path for destinations that were not present in its routing table.

## Resolution

The default route was restored:

    enable
    configure terminal
    ip route 0.0.0.0 0.0.0.0 10.50.1.250
    end

## Verification

The routing table was checked:

    show ip route

The default route was confirmed.

The next-hop router was also reachable:

    ping 10.50.1.250

## Packet Tracer Note

The ISP in this project is a simulated WAN device rather than a real Internet connection.

Therefore, successful forwarding toward the simulated ISP does not prove access to the real Internet.

## Lessons Learned

A default route is essential when a router or Layer 3 switch needs to forward traffic toward destinations that are not explicitly known.

## Skills Practiced

- Default routing
- Routing-table verification
- Next-hop testing
- `show ip route`
- Layer 3 troubleshooting

## Result

**Status: Resolved**

The DSW1 default route toward R1 was restored.
