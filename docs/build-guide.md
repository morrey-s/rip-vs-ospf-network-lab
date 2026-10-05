# Build Guide

This guide documents the final Cisco Packet Tracer topology used for the RIP v2 and OSPF comparison project.

## 1. Add the devices

Place:

- 3 x Cisco 2911 routers
- 2 x PCs

Rename the routers:

- R1
- R2
- R3

Rename the PCs:

- PC1
- PC2

## 2. Cable the topology

The final topology uses the following connections:

- PC1 to R1 G0/0
- R1 G0/1 to R2 G0/0
- R1 G0/2 to R3 G0/0
- R2 G0/1 to R3 G0/1
- R3 G0/2 to PC2

The overall layout is:

```text
PC1
 |
R1 -------- R2
 \          /
  \        /
     R3
      |
     PC2
```

Packet Tracer's automatic cabling option was used when building the topology. Auto-connect selected different router interfaces from those originally expected, so the final interface assignments above were verified manually before configuration.

When using auto-connect, always check the actual interfaces selected before assigning IP addresses.

## 3. Configure the PCs

PC1:

- IP address: `192.168.10.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.10.1`

PC2:

- IP address: `192.168.30.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.30.1`

## 4. Configure the router interfaces

Use the addressing plan in:

```text
docs/ip-addressing.md
```

After configuring each router, verify the interface assignments using:

```cisco
show ip interface brief
```

All interfaces used in the topology should show an operational state of `up/up`.

The final R3 interface assignments are especially important:

```text
R3 G0/0 = 10.0.13.2/30   (link to R1)
R3 G0/1 = 10.0.23.2/30   (link to R2)
R3 G0/2 = 192.168.30.1/24 (LAN connection to PC2)
```

## 5. Initial connectivity checks

Before configuring dynamic routing, verify each directly connected network.

From PC1:

```text
ping 192.168.10.1
```

From PC2:

```text
ping 192.168.30.1
```

Each PC should be able to reach its default gateway before RIP or OSPF testing begins.

## 6. Troubleshooting issue encountered

During initial testing, PC2 could not ping its default gateway even though the router interfaces appeared `up/up`.

The issue was caused by Packet Tracer's automatic cabling feature selecting different physical interfaces from those originally expected. The planned IP addresses had therefore been applied to the wrong R3 interfaces.

The problem was identified using:

```cisco
show ip interface brief
```

The physical cable connections were then checked against the configured interface addresses.

After moving the IP addresses to the correct interfaces, PC2 successfully reached its gateway.

This demonstrated that an interface being `up/up` does not necessarily mean the Layer 3 configuration is correct.

## 7. Configure RIP v2

Configure RIP v2 on R1, R2 and R3 using the files in:

```text
configs/rip/
```

Verify RIP using:

```cisco
show ip route
show ip protocols
```

RIP-learned routes should appear with the route code:

```text
R
```

## 8. Test RIP connectivity

From PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

The normal path should use the direct R1-R3 connection:

```text
PC1 -> R1 -> R3 -> PC2
```

Take screenshots of:

- The complete topology
- R1 `show ip route`
- A successful PC1-to-PC2 ping
- The normal traceroute

Save the working RIP topology as:

```text
packet-tracer/rip-network.pkt
```

## 9. Perform the RIP link-failure test

The direct R1-R3 connection uses R1 G0/2.

On R1:

```cisco
configure terminal
interface gigabitEthernet0/2
shutdown
end
```

Verify the failure:

```cisco
show ip interface brief
show ip route
```

Then repeat from PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

After RIP updates its routing information, the alternative path should be:

```text
PC1 -> R1 -> R2 -> R3 -> PC2
```

Take screenshots showing:

- R1 G0/2 down
- The updated routing table
- The failover traceroute

Restore the link:

```cisco
configure terminal
interface gigabitEthernet0/2
no shutdown
end
```

Save a copy as:

```text
packet-tracer/rip-failure-test.pkt
```

## 10. Create the OSPF version

Create a copy of the working RIP topology or rebuild the same network.

If converting the RIP file, remove RIP from each router:

```cisco
configure terminal
no router rip
end
```

Then apply the OSPF configurations from:

```text
configs/ospf/
```

Save the working OSPF version as:

```text
packet-tracer/ospf-network.pkt
```

## 11. Verify OSPF

Use:

```cisco
show ip route
show ip ospf neighbor
show ip protocols
```

Expected OSPF neighbour relationships should reach the `FULL` state.

Repeat the end-to-end tests from PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

OSPF-learned routes should appear with the route code:

```text
O
```

## 12. Perform the OSPF link-failure test

Again disable the direct R1-R3 link:

```cisco
configure terminal
interface gigabitEthernet0/2
shutdown
end
```

Check:

```cisco
show ip route
show ip ospf neighbor
```

Then from PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

The alternative path should again become:

```text
PC1 -> R1 -> R2 -> R3 -> PC2
```

Take screenshots showing:

- The failed R1-R3 link
- The changed OSPF neighbour information
- The updated routing table
- The failover traceroute

Restore the interface when finished:

```cisco
configure terminal
interface gigabitEthernet0/2
no shutdown
end
```

Save a copy as:

```text
packet-tracer/ospf-failure-test.pkt
```

## 13. Record the results

Complete:

```text
results/test-plan.md
results/comparison.md
```

Only record behaviour that was actually observed during the Packet Tracer tests.

If exact convergence times were not measured, describe the behaviour qualitatively rather than giving numerical timings.

## 14. Add screenshots

Store screenshots in:

```text
screenshots/
```

Use clear filenames such as:

```text
rip-routing-table.png
rip-tracert-failover.png
ospf-routing-table.png
ospf-neighbors.png
ospf-tracert-failover.png
```

## 15. Final repository check

Before publishing the project on GitHub:

- Confirm all `.pkt` files open correctly.
- Confirm the interface assignments match the final topology.
- Check that all screenshots are readable.
- Make sure the README links to the documentation and results files.
- Confirm the addressing table matches the actual Packet Tracer configuration.
