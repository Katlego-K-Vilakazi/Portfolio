# Dynamic Routing — EIGRP, OSPF & Route Redistribution

## Overview

This project demonstrates dynamic routing in a mixed-routing-protocol network using **EIGRP** and **OSPF** in Cisco Packet Tracer.

The internal network uses **EIGRP AS 100**, while the external network uses **OSPF process 1**. Router R3 acts as the boundary between the two routing domains and performs **route redistribution** between EIGRP and OSPF.

The final configuration was tested using end-to-end connectivity from **PC2** to `www.ralph.com`.

---

## Objectives

The objectives of this project were to:

* Configure router interfaces with the required IP addresses.
* Configure EIGRP on the internal network.
* Configure OSPF on the external network.
* Configure R3 to participate in both EIGRP and OSPF.
* Configure route redistribution between EIGRP and OSPF.
* Verify dynamically learned routes.
* Verify EIGRP neighbor relationships.
* Verify OSPF neighbor relationships.
* Test end-to-end connectivity.
* Confirm that PC2 can access `www.ralph.com`.

---

## Network Topology

The network is divided into two routing domains.

### Internal Network

The internal network uses **EIGRP AS 100**.

```text
                    EIGRP AS 100
                         
        R1 ---------------- R2_DHCP ---------------- R3
        |                    |                       |
        |                    |                       |
     Sales LAN          Accounting LAN           Admin LAN
  192.168.1.0/24       192.168.3.0/24         192.168.4.0/24
```

### External Network

The external side uses **OSPF process 1**.

```text
              OSPF Area 0

R3 ---------------- ISP_Router ---------------- Internet_Router
 |                     |                              |
 |                     |                              |
192.168.99.8/30    198.3.24.0/24                 192.168.5.0/24
                                                   |
                                              Internet Network
```

R3 is the boundary router between the two routing protocols.

---

## IP Addressing

### R1

| Interface          | IP Address   | Network         |
| ------------------ | ------------ | --------------- |
| GigabitEthernet0/0 | 192.168.99.1 | 192.168.99.0/30 |
| GigabitEthernet0/1 | 192.168.2.1  | 192.168.2.0/24  |
| GigabitEthernet0/2 | 192.168.1.1  | 192.168.1.0/24  |

### R2_DHCP

| Interface          | IP Address   | Network         |
| ------------------ | ------------ | --------------- |
| GigabitEthernet0/0 | 192.168.99.5 | 192.168.99.4/30 |
| GigabitEthernet0/1 | 192.168.99.2 | 192.168.99.0/30 |
| GigabitEthernet0/2 | 192.168.3.1  | 192.168.3.0/24  |

### R3

| Interface          | IP Address   | Network         |
| ------------------ | ------------ | --------------- |
| GigabitEthernet0/0 | 192.168.99.9 | 192.168.99.8/30 |
| GigabitEthernet0/1 | 192.168.99.6 | 192.168.99.4/30 |
| GigabitEthernet0/2 | 192.168.4.1  | 192.168.4.0/24  |

### ISP_Router

| Interface          | IP Address    |
| ------------------ | ------------- |
| GigabitEthernet0/1 | 192.168.99.10 |
| Serial0/0/0        | 198.3.24.3    |

### Internet_Router

| Interface       | IP Address  |
| --------------- | ----------- |
| FastEthernet0/0 | 192.168.5.1 |
| Serial2/0       | 198.3.24.4  |

---

# Routing Configuration

## EIGRP Configuration

EIGRP AS **100** was configured on the internal routers.

### R1

```cisco
enable
configure terminal

router eigrp 100
network 192.168.1.0 0.0.0.255
network 192.168.2.0 0.0.0.255
network 192.168.99.0 0.0.0.3
no auto-summary

end
copy running-config startup-config
```

R1 participates in EIGRP using the following networks:

* `192.168.1.0/24`
* `192.168.2.0/24`
* `192.168.99.0/30`

---

## R2_DHCP

```cisco
enable
configure terminal

router eigrp 100
network 192.168.3.0 0.0.0.255
network 192.168.99.0 0.0.0.3
network 192.168.99.4 0.0.0.3
no auto-summary

end
copy running-config startup-config
```

R2_DHCP participates in EIGRP using:

* `192.168.3.0/24`
* `192.168.99.0/30`
* `192.168.99.4/30`

---

## R3 EIGRP

R3 participates in EIGRP on the internal side of the network.

```cisco
enable
configure terminal

router eigrp 100
network 192.168.4.0
network 192.168.99.4 0.0.0.3
no auto-summary

end
copy running-config startup-config
```

R3 formed an EIGRP neighbor relationship with R2_DHCP.

---

# EIGRP Verification

## R2_DHCP EIGRP Neighbors

The following command was used:

```cisco
show ip eigrp neighbors
```

The resulting neighbor relationship was:

```text
Address         Interface
192.168.99.1    GigabitEthernet0/1
```

This confirmed that R2_DHCP successfully established an EIGRP adjacency with R1.

R3 also established an EIGRP adjacency with R2_DHCP:

```text
Address         Interface
192.168.99.5    GigabitEthernet0/1
```

---

## R2_DHCP Routing Table

The command:

```cisco
show ip route
```

showed EIGRP-learned routes including:

```text
D    192.168.1.0/24 [90/3072] via 192.168.99.1
D    192.168.2.0/24 [90/3072] via 192.168.99.1
```

The `D` routing code identifies routes learned through EIGRP.

---

# OSPF Configuration

OSPF process **1** was used on the external routing domain.

The OSPF network was configured as **Area 0**.

---

## Internet_Router OSPF

The original RIP configuration was removed and OSPF was configured.

```cisco
enable
configure terminal

router ospf 1
network 198.3.24.0 0.0.0.255 area 0
network 192.168.5.0 0.0.0.255 area 0

end
copy running-config startup-config
```

---

## ISP_Router OSPF

```cisco
enable
configure terminal

router ospf 1
log-adjacency-changes
network 192.168.99.8 0.0.0.3 area 0
network 198.3.24.0 0.0.0.255 area 0

end
copy running-config startup-config
```

---

## OSPF Serial Interface Configuration

The serial connection initially did not form an OSPF adjacency because of the serial OSPF network type.

Both serial interfaces were configured as point-to-point.

### ISP_Router

```cisco
enable
configure terminal

interface serial 0/0/0
ip ospf network point-to-point

end
```

### Internet_Router

```cisco
enable
configure terminal

interface serial 2/0
ip ospf network point-to-point

end
```

After this change, the OSPF adjacency reached the **FULL** state.

---

# OSPF Verification

The following command was used on ISP_Router:

```cisco
show ip ospf neighbor
```

The neighbor relationship was established with Internet_Router:

```text
Neighbor ID     State
198.3.24.4      FULL
```

The reverse relationship was also established on Internet_Router:

```text
Neighbor ID     State
198.3.24.3      FULL
```

This confirmed that the OSPF adjacency was operational.

---

## ISP_Router Routing Table

The following command was used:

```cisco
show ip route
```

The routing table contained the OSPF-learned route:

```text
O    192.168.5.0/24 [110/1563] via 198.3.24.4
```

The `O` routing code identifies a route learned through OSPF.

This demonstrated that ISP_Router was successfully learning the Internet network through OSPF.

---

# R3 — OSPF Configuration

R3 was configured to participate in OSPF on the external side.

```cisco
enable
configure terminal

router ospf 1
network 192.168.99.8 0.0.0.3 area 0
network 192.168.4.0 0.0.0.255 area 0

end
copy running-config startup-config
```

R3 successfully established an OSPF neighbor relationship with ISP_Router.

The neighbor was:

```text
Neighbor ID     State
198.3.24.3      FULL/DR
```

The neighbor address was:

```text
192.168.99.10
```

---

# Route Redistribution

R3 is the boundary between the EIGRP and OSPF routing domains.

Therefore, routes learned through one protocol need to be redistributed into the other protocol.

## EIGRP → OSPF

The following configuration was applied under EIGRP:

```cisco
router eigrp 100
redistribute ospf 1 metric 10000 100 255 1 1500
```

The EIGRP metric values used were:

```text
Bandwidth     10000
Delay         100
Reliability   255
Loading       1
MTU           1500
```

---

## OSPF → EIGRP

The following configuration was applied under OSPF:

```cisco
router ospf 1
redistribute eigrp 100 subnets
```

This allows EIGRP routes to be redistributed into OSPF.

---

## Complete R3 Redistribution Configuration

The relevant R3 configuration was:

```cisco
router eigrp 100
redistribute ospf 1 metric 10000 100 255 1 1500
network 192.168.4.0
network 192.168.99.4 0.0.0.3

router ospf 1
log-adjacency-changes
redistribute eigrp 100 subnets
network 192.168.99.8 0.0.0.3 area 0
network 192.168.4.0 0.0.0.255 area 0
```

---

# R3 Interface Verification

The command:

```cisco
show ip interface brief
```

was used to verify the R3 interfaces.

The final interface state was:

```text
Interface              IP-Address      Status    Protocol

GigabitEthernet0/0     192.168.99.9   up        up
GigabitEthernet0/1     192.168.99.6   up        up
GigabitEthernet0/2     192.168.4.1    up        up
```

All three required R3 interfaces were operational.

---

# R1 Route Verification

After redistribution was configured, R1 was checked using:

```cisco
show ip route
```

R1 learned the internal networks through EIGRP:

```text
D    192.168.3.0/24
D    192.168.4.0/24
```

R1 also learned external networks as EIGRP external routes:

```text
D EX 192.168.5.0/24
D EX 192.168.99.8/30
D EX 198.3.24.0/24
```

The `D EX` routes demonstrate that routes originating from the other routing domain were redistributed into EIGRP and then propagated toward R1.

In particular:

```text
D EX 192.168.5.0/24
```

provided R1 with a route toward the simulated Internet network.

---

# End-to-End Connectivity Test

The final connectivity test was performed from **PC2**.

PC2 accessed:

```text
http://www.ralph.com
```

The webpage successfully opened.

This demonstrated end-to-end connectivity across the network:

```text
PC2
 |
 | 192.168.3.0/24
 |
R2_DHCP
 |
 | EIGRP
 |
R3
 |
 | Route Redistribution
 |
ISP_Router
 |
 | OSPF
 |
Internet_Router
 |
 | 192.168.5.0/24
 |
www.ralph.com
```

---

# Troubleshooting

Several routing issues were encountered during configuration.

## 1. OSPF Was Not Active on the Serial Interface

Initially, the OSPF configuration used:

```cisco
network 198.3.24.0 0.0.0.3 area 0
```

However, the serial interfaces in the Packet Tracer topology were configured with addresses in a `/24` network.

The OSPF network statement was therefore changed to:

```cisco
network 198.3.24.0 0.0.0.255 area 0
```

This allowed OSPF to become active on the serial interface.

---

## 2. OSPF Adjacency Was Not Forming

Even after OSPF was enabled on the serial interfaces, the routers did not initially establish a neighbor relationship.

The serial OSPF network type was changed to point-to-point:

```cisco
interface serial 0/0/0
ip ospf network point-to-point
```

and:

```cisco
interface serial 2/0
ip ospf network point-to-point
```

After the change, both routers reported the OSPF neighbor as:

```text
FULL
```

---

## 3. Verifying Redistribution

After configuring redistribution on R3, the routing table on R1 was checked.

The presence of:

```text
D EX 192.168.5.0/24
```

confirmed that the external network had been redistributed into EIGRP and was reachable from the internal network.

---

# Useful Verification Commands

The following commands were useful throughout the project.

### Interface Status

```cisco
show ip interface brief
```

### Routing Table

```cisco
show ip route
```

### EIGRP Neighbors

```cisco
show ip eigrp neighbors
```

### EIGRP Configuration

```cisco
show ip protocols
```

### OSPF Neighbors

```cisco
show ip ospf neighbor
```

### OSPF Interface Information

```cisco
show ip ospf interface
```

### Routing Protocol Configuration

```cisco
show running-config | section router
```

### Save Configuration

```cisco
copy running-config startup-config
```

---

# Routing Codes Used

| Code   | Meaning              |
| ------ | -------------------- |
| `C`    | Connected            |
| `L`    | Local                |
| `D`    | EIGRP                |
| `D EX` | EIGRP External       |
| `O`    | OSPF                 |
| `S`    | Static               |
| `S*`   | Static default route |

Understanding these codes makes it easier to determine where a route came from when troubleshooting.

---

# Key Lessons Learned

### 1. Different routing protocols can operate in the same network

EIGRP and OSPF can be used in separate routing domains within the same topology.

### 2. Redistribution is required between routing domains

R3 acts as the boundary router and passes routing information between EIGRP and OSPF.

### 3. Neighbor relationships must be established

Dynamic routing protocols depend on routers successfully communicating with their neighbors.

For EIGRP, this was verified using:

```cisco
show ip eigrp neighbors
```

For OSPF, this was verified using:

```cisco
show ip ospf neighbor
```

### 4. Routing tables show where routes came from

The routing table can be used to identify whether a route is:

* Directly connected
* EIGRP learned
* EIGRP external
* OSPF learned
* Static

### 5. Interface configuration affects routing protocols

The OSPF troubleshooting process demonstrated that the routing protocol configuration has to match the actual interface/network configuration.

### 6. End-to-end testing is important

A routing table can look correct while an application still fails. The final browser test from PC2 to `www.ralph.com` provided an application-level confirmation that the network was working.

---

# Project Files

The main Packet Tracer project file is:

```text
Dynamic_Routing_Project.pkt
```

Recommended GitHub repository structure:

```text
dynamic-routing/
│
├── README.md
│
├── Evidence/
│   ├── r3-interface-status.png
│   ├── r2-eigrp-routing-table.png
│   ├── r2-eigrp-neighbors.png
│   ├── isp-ospf-routing-table.png
│   ├── r3-redistribution.png
│   ├── r1-routing-table.png
│   └── pc2-ralph-website.png
│
└── Packet_Tracer/
    └── Dynamic_Routing_Project.pkt
```
---

# Skills Demonstrated

This project demonstrates practical experience with:

* Cisco IOS
* Cisco Packet Tracer
* IPv4 addressing
* Subnetting
* Router interface configuration
* EIGRP
* OSPF
* EIGRP neighbor relationships
* OSPF neighbor relationships
* Dynamic routing
* Route redistribution
* Routing table analysis
* OSPF troubleshooting
* EIGRP troubleshooting
* End-to-end connectivity testing
* DNS/web connectivity testing
* Network troubleshooting methodology

---

# Verification Summary

| Component                     | Result            |
| ----------------------------- | ----------------- |
| R3 interfaces                 | Up/Up             |
| R1 EIGRP                      | Operational       |
| R2_DHCP EIGRP                 | Operational       |
| R3 EIGRP                      | Operational       |
| R1 ↔ R2 EIGRP neighbor        | Established       |
| R2 ↔ R3 EIGRP neighbor        | Established       |
| ISP_Router OSPF               | Operational       |
| Internet_Router OSPF          | Operational       |
| R3 OSPF                       | Operational       |
| R3 ↔ ISP_Router OSPF neighbor | FULL              |
| R3 route redistribution       | Configured        |
| R1 external routes            | Learned as `D EX` |
| PC2 → `www.ralph.com`         | Successful        |

---

# Conclusion

This project demonstrated how a network can use different dynamic routing protocols in different parts of the infrastructure.

EIGRP was used for the internal network, while OSPF was used for the external network. R3 provided the connection between the two routing domains by participating in both protocols and redistributing routes between them.

The configuration was verified using routing tables, EIGRP and OSPF neighbor information, interface status, and an end-to-end browser test.

The final test from PC2 successfully reached:

```text
www.ralph.com
```

This confirmed that the routing configuration and route redistribution allowed traffic to travel across the complete network.
