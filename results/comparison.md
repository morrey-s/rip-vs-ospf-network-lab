# RIP v2 vs OSPF - Practical Comparison

This document records the results of practical testing carried out in Cisco Packet Tracer using the same three-router topology for both RIP v2 and OSPF.

## Baseline Connectivity

### RIP v2

After configuring RIP v2 on R1, R2 and R3, end-to-end connectivity was successfully established between PC1 on `192.168.10.0/24` and PC2 on `192.168.30.0/24`.

R1 dynamically learned the remote `192.168.30.0/24` network through RIP. Under normal conditions, traffic used the direct R1-R3 link because this provided the shortest path to the destination network.

The normal path was:

```text
PC1 -> R1 -> R3 -> PC2
```

The RIP route was visible in the routing table using:

```cisco
show ip route
```

RIP-learned routes were identified by the route code `R`.

### OSPF

After replacing RIP with OSPF and placing the relevant networks in area 0, end-to-end connectivity between PC1 and PC2 was again successful.

R1 dynamically learned the `192.168.30.0/24` network through OSPF. Under normal conditions, traffic again used the direct R1-R3 link.

The normal path was:

```text
PC1 -> R1 -> R3 -> PC2
```

The OSPF route was visible using:

```cisco
show ip route
```

OSPF-learned routes were identified by the route code `O`.

The OSPF neighbour relationships were also verified using:

```cisco
show ip ospf neighbor
```

## Link Failure

The resilience test simulated failure of the direct R1-R3 connection by shutting down R1 `GigabitEthernet0/2`.

```cisco
configure terminal
interface gigabitEthernet0/2
shutdown
end
```

This removed the normal direct path between R1 and R3 and forced the routing protocols to use the remaining path through R2.

### RIP v2 observations

When the direct R1-R3 link was disabled, RIP detected that the original route was no longer available.

Connectivity was temporarily affected while the routing information updated. After RIP reconverged, R1 learned an alternative route to the `192.168.30.0/24` network through R2.

The failover path became:

```text
PC1 -> R1 -> R2 -> R3 -> PC2
```

A new `tracert` from PC1 confirmed that traffic was travelling through the additional router rather than using the failed direct R1-R3 link.

The exact convergence time was not formally measured during the lab, so no numerical timing is claimed here. Visually, RIP took longer to update than OSPF during the same failure test.

After the R1-R3 interface was restored with `no shutdown`, RIP eventually returned to the direct R1-R3 path.

### OSPF observations

The same link-failure test was repeated using OSPF.

When R1 `GigabitEthernet0/2` was shut down, the OSPF topology changed and the direct adjacency across the R1-R3 link was lost.

OSPF recalculated the available path and traffic was able to use:

```text
PC1 -> R1 -> R2 -> R3 -> PC2
```

The updated route could be seen in the routing table, and `tracert` confirmed that packets were being forwarded through R2.

The exact convergence time was not formally timed. However, during the practical test OSPF appeared to react to the topology change more quickly than RIP.

After restoring the R1-R3 interface, the OSPF neighbour relationship was re-established and the direct path became available again.

## Troubleshooting Example - Incorrect Interface Addressing

During initial connectivity testing, PC2 was unable to reach its default gateway even though the relevant router interfaces showed an `up/up` state.

Packet Tracer's automatic cabling option had been used when connecting the devices. This selected different physical router interfaces than originally expected, while the planned IP addresses had been configured against the assumed interfaces.

I used:

```cisco
show ip interface brief
```

and checked the actual cable connections and interface addressing. This identified that the correct IP addresses had been assigned to the wrong physical interfaces.

After correcting the interface IP assignments, PC2 was able to successfully ping its default gateway and end-to-end testing could continue.

This demonstrated that an interface being operational at the physical and data-link layers does not guarantee that the Layer 3 configuration is correct, and reinforced the importance of verifying the actual interfaces selected by Packet Tracer.

## Comparison

| Area | RIP v2 | OSPF |
|---|---|---|
| Initial configuration | Simpler | More detailed |
| Routing approach | Distance-vector | Link-state |
| Metric | Hop count | Cost |
| Route code | `R` | `O` |
| Neighbour visibility | Limited | Detailed with `show ip ospf neighbor` |
| Normal path | R1 -> R3 | R1 -> R3 |
| Failure path | R1 -> R2 -> R3 | R1 -> R2 -> R3 |
| Failure behaviour observed | Route updated after convergence and traffic used R2 | Topology recalculated and traffic used R2 |
| Recovery behaviour observed | Direct route returned after the interface was restored | Adjacency and direct route returned after the interface was restored |
| Practical troubleshooting visibility | Basic routing information | More detailed protocol and neighbour information |

## Conclusion

RIP v2 was easier to configure and provided a straightforward introduction to dynamic routing. OSPF required more configuration, including router IDs, wildcard masks and neighbour relationships, but it also provided more detailed information for verifying and troubleshooting the network.

The link-failure test demonstrated why redundant network paths are useful. When the direct R1-R3 connection was disabled, both routing protocols were able to use the alternative R1-R2-R3 path after updating their routing information. In this lab, OSPF appeared to react more quickly to the topology change, while RIP took longer to reconverge. The exercise also reinforced the importance of systematic troubleshooting, particularly checking interface addressing and physical interface assignments rather than assuming that an `up/up` interface is correctly configured.
