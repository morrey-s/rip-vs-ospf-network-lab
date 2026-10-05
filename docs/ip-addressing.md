# IP Addressing

The following addressing plan reflects the final interface assignments used in the Packet Tracer topology.

| Device | Interface | IP address | Subnet mask | Network |
|---|---|---:|---:|---|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | 192.168.10.0/24 |
| R1 | G0/1 | 10.0.12.1 | 255.255.255.252 | 10.0.12.0/30 |
| R1 | G0/2 | 10.0.13.1 | 255.255.255.252 | 10.0.13.0/30 |
| R2 | G0/0 | 10.0.12.2 | 255.255.255.252 | 10.0.12.0/30 |
| R2 | G0/1 | 10.0.23.1 | 255.255.255.252 | 10.0.23.0/30 |
| R3 | G0/0 | 10.0.13.2 | 255.255.255.252 | 10.0.13.0/30 |
| R3 | G0/1 | 10.0.23.2 | 255.255.255.252 | 10.0.23.0/30 |
| R3 | G0/2 | 192.168.30.1 | 255.255.255.0 | 192.168.30.0/24 |
| PC1 | NIC | 192.168.10.10 | 255.255.255.0 | 192.168.10.0/24 |
| PC2 | NIC | 192.168.30.10 | 255.255.255.0 | 192.168.30.0/24 |

## Default Gateways

PC1:

```text
192.168.10.1
```

PC2:

```text
192.168.30.1
```

## Point-to-Point Router Links

```text
R1 G0/1 <-> R2 G0/0
10.0.12.1     10.0.12.2
Network: 10.0.12.0/30
```

```text
R1 G0/2 <-> R3 G0/0
10.0.13.1     10.0.13.2
Network: 10.0.13.0/30
```

```text
R2 G0/1 <-> R3 G0/1
10.0.23.1     10.0.23.2
Network: 10.0.23.0/30
```

## LAN Networks

LAN 1:

```text
Network: 192.168.10.0/24
Gateway: 192.168.10.1
PC1:     192.168.10.10
```

LAN 2:

```text
Network: 192.168.30.0/24
Gateway: 192.168.30.1
PC2:     192.168.30.10
```

## Note on Interface Assignment

Packet Tracer's automatic cabling option selected different R3 interfaces from those originally expected.

The final verified R3 assignments are:

```text
G0/0 -> R1
G0/1 -> R2
G0/2 -> PC2
```

These assignments were confirmed using:

```cisco
show ip interface brief
```
