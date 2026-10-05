# RIP v2 vs OSPF Network Lab

## Overview

This project is a Cisco Packet Tracer lab comparing RIP v2 and OSPF in the same three-router network.

It was adapted from work completed during my BSc (Hons) Computing and IT (Networking) degree and is presented here as a practical portfolio project focused on configuration, verification, fault testing and troubleshooting.

The lab uses a redundant triangular router topology so that a link can be deliberately disabled and the behaviour of each routing protocol can be observed.

## Objectives

- Build a three-router Cisco Packet Tracer network.
- Configure IPv4 addressing on routers and end devices.
- Configure and verify RIP version 2.
- Configure and verify OSPF area 0.
- Test end-to-end connectivity.
- Inspect routing tables and protocol-specific information.
- Simulate a link failure.
- Confirm that an alternative route is learned and used.
- Compare RIP v2 and OSPF from a practical troubleshooting perspective.

## Technologies Used

- Cisco Packet Tracer
- Cisco 2911 routers
- Cisco IOS
- IPv4
- RIP version 2
- OSPF
- ICMP
- Cisco IOS troubleshooting commands

## Topology

```text
        10.0.12.0/30
      R1-------------R2
      |               |
      |               |
      |               |
      |               |
      R3--------------+
      10.0.13.0/30   10.0.23.0/30

LAN 1: 192.168.10.0/24
PC1 --- R1

LAN 2: 192.168.30.0/24
R3 --- PC2
```

The direct R1-R3 link is normally the shortest path between the two LANs. During failure testing, that link is disabled so traffic must travel through R2.

## Addressing Plan

| Device | Interface | IP address | Subnet mask | Purpose |
|---|---|---:|---:|---|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | LAN 1 gateway |
| R1 | G0/1 | 10.0.12.1 | 255.255.255.252 | Link to R2 |
| R1 | G0/2 | 10.0.13.1 | 255.255.255.252 | Link to R3 |
| R2 | G0/0 | 10.0.12.2 | 255.255.255.252 | Link to R1 |
| R2 | G0/1 | 10.0.23.1 | 255.255.255.252 | Link to R3 |
| R3 | G0/0 | 192.168.30.1 | 255.255.255.0 | LAN 2 gateway |
| R3 | G0/1 | 10.0.23.2 | 255.255.255.252 | Link to R2 |
| R3 | G0/2 | 10.0.13.2 | 255.255.255.252 | Link to R1 |
| PC1 | NIC | 192.168.10.10 | 255.255.255.0 | Test host |
| PC2 | NIC | 192.168.30.10 | 255.255.255.0 | Test host |

Default gateway for PC1: `192.168.10.1`

Default gateway for PC2: `192.168.30.1`

## Repository Structure

```text
routing-protocol-comparison/
├── README.md
├── docs/
│   ├── build-guide.md
│   └── ip-addressing.md
├── packet-tracer/
│   └── README.md
├── configs/
│   ├── rip/
│   │   ├── R1.txt
│   │   ├── R2.txt
│   │   └── R3.txt
│   └── ospf/
│       ├── R1.txt
│       ├── R2.txt
│       └── R3.txt
├── screenshots/
│   └── README.md
└── results/
    ├── comparison.md
    └── test-plan.md
```

## RIP v2

The RIP configuration uses version 2 and disables automatic summarisation.

Example:

```cisco
router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 192.168.10.0
```

Useful verification commands:

```cisco
show ip route
show ip protocols
show ip interface brief
```

RIP-learned routes are identified by `R` in the routing table.

Full device configurations are in `configs/rip/`.

## OSPF

All router-to-router and LAN networks are placed in OSPF area 0.

Example:

```cisco
router ospf 1
 router-id 1.1.1.1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.13.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

Useful verification commands:

```cisco
show ip route
show ip ospf neighbor
show ip ospf interface brief
show ip protocols
```

OSPF-learned routes are identified by `O` in the routing table.

Full device configurations are in `configs/ospf/`.

## Connectivity Testing

From PC1:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

From PC2:

```text
ping 192.168.10.10
tracert 192.168.10.10
```

Successful tests confirm that the routers are learning remote networks and forwarding traffic correctly.

## Failure Test

The main resilience test disables the direct R1-R3 connection.

On R1:

```cisco
enable
configure terminal
interface gigabitEthernet0/2
shutdown
end
```

Then repeat:

```text
ping 192.168.30.10
tracert 192.168.30.10
```

The routing table should also be checked:

```cisco
show ip route
```

Traffic should eventually use the alternative path:

```text
R1 -> R2 -> R3
```

Restore the interface using:

```cisco
configure terminal
interface gigabitEthernet0/2
no shutdown
end
```

The same test can be performed once with RIP v2 and once with OSPF.

## Troubleshooting Approach

The following commands are used throughout the project:

```cisco
show ip interface brief
show ip route
show running-config
show ip protocols
show ip ospf neighbor
ping
traceroute
```

When connectivity fails, the troubleshooting order is:

1. Check interface status.
2. Check IPv4 addresses and subnet masks.
3. Check end-device default gateways.
4. Check directly connected routes.
5. Check whether dynamic routes have been learned.
6. Check protocol configuration.
7. Check OSPF neighbour relationships where applicable.
8. Use ping and traceroute to locate the failure.

## RIP v2 vs OSPF

| Feature | RIP v2 | OSPF |
|---|---|---|
| Routing approach | Distance-vector | Link-state |
| Metric | Hop count | Cost |
| Maximum hop count | 15 | Not based on a hop-count limit |
| Configuration | Simpler | More detailed |
| Topology awareness | Limited | Builds a link-state database |
| Scalability | Smaller networks | Better suited to larger networks |
| Route code | R | O |

The practical results from the lab should be recorded in `results/comparison.md`.

## Skills Demonstrated

- Cisco router configuration
- IPv4 addressing and subnetting
- Dynamic routing
- RIP v2
- OSPF
- Routing-table analysis
- Connectivity testing
- Fault simulation
- Network troubleshooting
- Technical documentation
- Problem solving

### Troubleshooting Example – Incorrect Interface Addressing

During initial connectivity testing, PC2 was unable to reach its default gateway even though the router interfaces showed an `up/up` status.

I had used Packet Tracer’s automatic cabling option when connecting the devices, which resulted in different physical router interfaces being selected than I originally expected. I had then assigned the planned IP addresses to the wrong interfaces on R3.

I used `show ip interface brief` and checked the actual interface connections and addressing. This identified the mismatch between the configured IP addresses and the interfaces being used.

After correcting the interface IP assignments, I tested connectivity again and PC2 was able to successfully ping its default gateway.

This was a useful reminder to verify the actual interfaces selected by Packet Tracer rather than assuming the automatically chosen ports match the original plan.

## What I Learned

This project strengthened my understanding of how routers learn remote networks and how different routing protocols react when the topology changes.

The most useful part of the lab was deliberately introducing a link failure and troubleshooting the resulting route changes. This required checking interface state, addressing, routing tables and routing-protocol information rather than relying only on ping results.

## Future Improvements

- Measure convergence more precisely.
- Add VLANs and inter-VLAN routing.
- Add DHCP.
- Add ACLs.
- Expand the topology.
- Test multiple OSPF areas.
- Add packet captures or Packet Tracer simulation-mode screenshots.

## Author

**Sam Morrey**

BSc (Hons) Computing and IT (Networking)

Interested in networking, IT support, troubleshooting and cybersecurity.
