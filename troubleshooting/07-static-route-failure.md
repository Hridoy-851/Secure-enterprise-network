# Incident 07 — Static Route Failure

## Scenario

Traffic from the enterprise network could not reach one or more internal VLANs because a required static route was missing from R1.

## Affected Device

- **Device:** R1
- **Role:** Enterprise edge router
- **Next Hop:** 10.50.1.249
- **Destination:** Enterprise VLAN networks

## Symptoms

Traffic entering the network through R1 could not reach the affected internal subnet.

The Layer 3 link between R1 and DSW1 was operational.

## Investigation

The routing table was checked:

    show ip route

The required enterprise route was missing.

The R1-to-DSW1 connection was also verified:

    ping 10.50.1.249

The next-hop device responded successfully.

## Root Cause

A required static route pointing toward DSW1 was missing from R1.

R1 therefore did not have a route for the affected enterprise subnet.

## Resolution

The missing route was restored using the appropriate enterprise next hop.

Example:

    ip route 10.50.0.128 255.255.255.224 10.50.1.249

The remaining enterprise VLAN routes were also verified.

## Verification

The routing table was checked again:

    show ip route

The required route appeared as a static route.

Connectivity through the restored route was then tested.

## Lessons Learned

A working physical link does not guarantee Layer 3 reachability.

When a routed path fails, verify:

1. Interface status.
2. Next-hop reachability.
3. Routing table.
4. Static or dynamic route configuration.
5. End-to-end connectivity.

## Skills Practiced

- Static routing
- Routing-table analysis
- Next-hop verification
- `show ip route`
- Layer 3 troubleshooting

## Result

**Status: Resolved**

The missing static route was restored and enterprise network reachability was recovered.
