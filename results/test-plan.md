# Test Plan

This test plan records the checks performed on both the RIP v2 and OSPF versions of the Cisco Packet Tracer lab.

| Test | RIP v2 result | OSPF result |
|---|---|---|
| All required interfaces up/up | Pass | Pass |
| PC1 can ping R1 gateway | Pass | Pass |
| PC2 can ping R3 gateway | Pass after correcting interface addressing | Pass |
| PC1 can ping PC2 | Pass | Pass |
| PC2 can ping PC1 | Pass | Pass |
| R1 learns `192.168.30.0/24` dynamically | Pass - RIP route visible | Pass - OSPF route visible |
| R3 learns `192.168.10.0/24` dynamically | Pass - RIP route visible | Pass - OSPF route visible |
| Failure of R1 G0/2 detected | Pass | Pass |
| Alternate R1-R2-R3 path becomes available | Pass | Pass |
| Connectivity returns after reconvergence | Pass | Pass |
| Direct route returns after interface restoration | Pass | Pass |

## Baseline Verification

Before testing a link failure, the following commands were used to confirm interface state and routing information.

On R1:

```cisco
show ip interface brief
show ip route
show ip protocols
```

For OSPF, additional verification included:

```cisco
show ip ospf neighbor
show ip ospf interface brief
```

From PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

The normal path between the two LANs was:

```text
PC1 -> R1 -> R3 -> PC2
```

## Initial Troubleshooting

During the first connectivity test, PC2 could not ping its default gateway.

All relevant interfaces appeared operational, but inspection of the actual Packet Tracer connections and the output of:

```cisco
show ip interface brief
```

showed that IP addresses had been assigned to the wrong physical interfaces.

This happened because Packet Tracer's automatic cabling option selected different router interfaces than originally expected.

The IP assignments were corrected to match the actual interfaces being used. After this change, PC2 successfully reached its gateway and the remaining tests could continue.

## Failure Procedure

The direct R1-R3 link was disabled on R1:

```cisco
configure terminal
interface gigabitEthernet0/2
shutdown
end
```

The interface state and routing table were then checked:

```cisco
show ip interface brief
show ip route
```

For OSPF:

```cisco
show ip ospf neighbor
```

A new test was performed from PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

After the routing protocol updated, the alternative path was:

```text
PC1 -> R1 -> R2 -> R3 -> PC2
```

This confirmed that the network could continue forwarding traffic using the redundant path.

## RIP v2 Failure Result

RIP detected the unavailable direct R1-R3 route and, after convergence, installed an alternative route through R2.

The failover traceroute showed the additional hop through R2.

The exact convergence time was not measured, so the test records the result as a successful route change rather than assigning a numerical convergence value.

## OSPF Failure Result

OSPF detected the topology change after the R1-R3 link was disabled.

The direct OSPF neighbour relationship on the failed link was lost, and OSPF recalculated the route using R2.

The failover traceroute confirmed:

```text
R1 -> R2 -> R3
```

OSPF appeared to react more quickly than RIP during the practical test, although exact convergence timing was not formally measured.

## Recovery Procedure

The direct R1-R3 link was restored:

```cisco
configure terminal
interface gigabitEthernet0/2
no shutdown
end
```

The routing table was checked again:

```cisco
show ip route
```

For OSPF:

```cisco
show ip ospf neighbor
```

The direct R1-R3 path became available again and normal connectivity was confirmed with:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

## Overall Result

Both RIP v2 and OSPF successfully provided dynamic routing between the two LANs and both were able to use the redundant R1-R2-R3 path when the direct R1-R3 connection was unavailable.

The testing also demonstrated the importance of checking Layer 3 configuration even when interfaces report an operational `up/up` state.
