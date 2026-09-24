# RIP v2 vs OSPF Network Lab

## Overview

This project is a Cisco Packet Tracer networking lab comparing the configuration and behaviour of RIP v2 and OSPF.

The project was originally developed as part of my Computing and IT (Networking) degree and has been adapted into a practical portfolio project to demonstrate my networking, configuration, testing and troubleshooting skills.

The same network environment was configured using both routing protocols so that their operation could be tested and compared.

## Objectives

The main objectives of the project were to:

* Design and configure a routed network in Cisco Packet Tracer.
* Configure IPv4 addressing across routers and end devices.
* Implement RIP v2 as a dynamic routing protocol.
* Implement OSPF as a dynamic routing protocol.
* Verify that routes were being learned correctly.
* Test connectivity between different networks.
* Introduce network failures and observe how the network responded.
* Use Cisco IOS commands to diagnose connectivity and routing problems.
* Compare the behaviour and configuration of RIP v2 and OSPF.

## Technologies Used

* Cisco Packet Tracer
* Cisco IOS
* Cisco 2911 routers
* IPv4
* RIP version 2
* OSPF
* ICMP
* Command-line network troubleshooting tools

## Network Topology

The lab contains multiple routed networks connected using Cisco routers.

The same basic topology was used for both the RIP v2 and OSPF configurations so that the two routing protocols could be compared under similar conditions.

Add topology image here:

```text
screenshots/network-topology.png
```

The network contains:

* Multiple Cisco routers
* Separate IPv4 networks
* End devices used to test connectivity
* Multiple router-to-router links
* Dynamic routing between networks

## IP Addressing

Each router interface and end device was assigned a static IPv4 address.

An addressing table can be added here:

| Device | Interface   | IP Address | Subnet Mask |
| ------ | ----------- | ---------- | ----------- |
| R1     | G0/0        | [Add IP]   | [Add mask]  |
| R1     | [Interface] | [Add IP]   | [Add mask]  |
| R2     | [Interface] | [Add IP]   | [Add mask]  |
| R3     | [Interface] | [Add IP]   | [Add mask]  |
| PC1    | NIC         | [Add IP]   | [Add mask]  |
| PC2    | NIC         | [Add IP]   | [Add mask]  |

## RIP v2 Configuration

The first version of the network used RIP version 2.

RIP was configured on each router and the appropriate directly connected networks were advertised.

Example configuration:

```cisco
router rip
 version 2
 no auto-summary
 network [network-address]
 network [network-address]
```

After configuration, the routing tables were checked to confirm that RIP routes had been learned.

Useful verification command:

```cisco
show ip route
```

RIP-learned routes appear in the routing table with the `R` code.

Additional configuration files for individual routers are available in the `configs/rip` directory.

## OSPF Configuration

The network was then configured using OSPF.

Each router was configured to participate in the OSPF routing process and advertise the required networks.

Example configuration:

```cisco
router ospf 1
 network [network-address] [wildcard-mask] area 0
```

OSPF operation was verified using commands including:

```cisco
show ip route
show ip ospf neighbor
show ip ospf interface
```

OSPF-learned routes appear in the routing table with the `O` code.

Individual router configurations are available in the `configs/ospf` directory.

## Connectivity Testing

Once each routing protocol had been configured, connectivity was tested between devices located on different networks.

Testing included:

```text
ping
tracert
```

Successful pings confirmed that traffic could travel through the routers and reach networks that were not directly connected.

Routing tables were also inspected to confirm that routers had learned the expected routes.

## Troubleshooting

A major part of the project involved deliberately introducing faults into the network and investigating their effect.

Rather than only demonstrating a working configuration, I wanted the project to show how I would approach a networking problem when something goes wrong.

Commands used during troubleshooting included:

```cisco
show ip interface brief
show ip route
show running-config
show ip ospf neighbor
ping
traceroute
```

These commands helped identify issues involving:

* Interface status
* IP addressing
* Routing configuration
* Learned routes
* OSPF neighbour relationships
* End-to-end connectivity

## Link Failure Test

One of the tests involved deliberately shutting down a router interface to simulate a network failure.

For example:

```cisco
interface GigabitEthernet0/0
 shutdown
```

The network was then monitored to determine how the routing environment responded to the unavailable link.

I checked:

* Whether connectivity was lost
* Which routes disappeared or changed
* Whether another available path was used
* Changes to the routing table
* The behaviour of the routing protocol following the failure

The interface could then be restored using:

```cisco
interface GigabitEthernet0/0
 no shutdown
```

Further tests were performed after restoring the interface to confirm that normal connectivity returned.

## RIP v2 and OSPF Comparison

| Feature                    | RIP v2                                  | OSPF                             |
| -------------------------- | --------------------------------------- | -------------------------------- |
| Routing type               | Distance-vector                         | Link-state                       |
| Metric                     | Hop count                               | Cost                             |
| Configuration              | Relatively simple                       | More detailed                    |
| Maximum hop count          | 15                                      | Not based on hop-count limit     |
| Network knowledge          | Learns routes from neighbouring routers | Builds a topology database       |
| Scalability                | Better suited to smaller networks       | Better suited to larger networks |
| Troubleshooting complexity | Lower                                   | Higher                           |
| Route identifier           | R                                       | O                                |

Both protocols were capable of providing dynamic routing within the test environment, although their method of calculating and maintaining routes differs significantly.

RIP v2 was straightforward to configure and useful for demonstrating the basic operation of dynamic routing.

OSPF required more configuration and understanding, but provided more advanced routing behaviour and is more suitable for larger or more complex networks.

## Repository Structure

```text
routing-protocol-comparison/
│
├── README.md
│
├── packet-tracer/
│   ├── rip-network.pkt
│   ├── ospf-network.pkt
│   └── failure-test.pkt
│
├── configs/
│   ├── rip/
│   │   ├── R1.txt
│   │   ├── R2.txt
│   │   └── R3.txt
│   │
│   └── ospf/
│       ├── R1.txt
│       ├── R2.txt
│       └── R3.txt
│
├── screenshots/
│   ├── network-topology.png
│   ├── rip-routing-table.png
│   ├── ospf-routing-table.png
│   └── failure-test.png
│
└── results/
    └── comparison.md
```

## Skills Demonstrated

This project demonstrates practical experience with:

* Cisco router configuration
* IPv4 addressing and subnetting
* Dynamic routing
* RIP v2
* OSPF
* Cisco IOS
* Routing tables
* Network connectivity testing
* Fault simulation
* Network troubleshooting
* Technical documentation
* Problem solving

## What I Learned

This project improved my understanding of how dynamic routing protocols allow routers to exchange information and automatically build routes to remote networks.

It also gave me practical experience using Cisco IOS commands to investigate network behaviour rather than relying only on whether a ping succeeded or failed.

The failure testing was particularly useful because it required me to work through a problem systematically by checking interface status, addressing, routing tables and protocol configuration.

Comparing RIP v2 and OSPF also helped me understand the difference between simply configuring a routing protocol and understanding how that protocol responds when the network changes.

## Future Improvements

Possible future extensions to this project include:

* Adding a larger network topology.
* Testing multiple OSPF areas.
* Comparing convergence following different link failures.
* Introducing VLANs and inter-VLAN routing.
* Adding DHCP services.
* Implementing access control lists.
* Capturing additional troubleshooting scenarios.
* Testing network redundancy with alternative paths.

## Author

**Sam Morrey**

BSc (Hons) Computing and IT (Networking) graduate

Interested in networking, IT support, troubleshooting and cybersecurity.
