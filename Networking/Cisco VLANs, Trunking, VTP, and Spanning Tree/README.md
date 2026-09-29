# Cisco VLANs, Trunking, VTP & Spanning Tree Lab

## Overview

This project is a Cisco Packet Tracer networking lab focused on **VLAN segmentation, trunking, VTP, and Spanning Tree Protocol (STP)**.

The lab models a small three-team office network. Each team has a Cisco Catalyst 2960 switch and three workstations representing the Sales, Marketing, and Administration departments.

The project begins with a flat Layer 2 network where all workstations share the same IPv4 subnet. The network is then redesigned using VLANs, trunk links, VTP, and STP.

The final design demonstrates:

- VLAN-based network segmentation
- 802.1Q trunking between switches
- VTP Server/Client operation
- STP loop prevention and reconvergence
- Same-VLAN communication across multiple switches
- Isolation between different VLANs without a router
- Restricting a VLAN from trunk links

> **Platform:** Cisco Packet Tracer  
> **Switch model:** Cisco Catalyst 2960  
> **IOS:** Cisco IOS used by the Packet Tracer switch model  
> **Routing:** No router / no inter-VLAN routing

---

## Project Objectives

The main objectives of this lab were to:

1. Verify connectivity across the initial flat Layer 2 network.
2. Observe the effect of a switching loop.
3. Enable Spanning Tree Protocol and observe loop prevention.
4. Configure redundant switch-to-switch links.
5. Configure GigabitEthernet links as trunks.
6. Configure VTP with one Server and two Clients.
7. Create Sales, Marketing, and Administration VLANs.
8. Assign access ports to the correct VLANs.
9. Propagate VLAN information through VTP.
10. Readdress the workstations according to their VLANs.
11. Verify same-VLAN connectivity.
12. Verify that different VLANs cannot communicate without Layer 3 routing.
13. Restrict VLAN 10 from the Team1 trunk links.

---

## Network Topology

The topology consists of three office/team sections:

```text
                         +------------------+
                         |   Team1 Switch   |
                         |  Team1_Switch    |
                         |   VTP Server     |
                         +--------+---------+
                            Gi0/1 | Gi0/2
                                  |
                    +-------------+-------------+
                    |                           |
              Gi0/1/2                      Gi0/1/2
            +---------+                  +---------+
            | Team2   |                  | Team3   |
            | Switch  |                  | Switch  |
            | Client  |                  | Client  |
            +---------+                  +---------+
                |                            |
             PCs 1-3                      PCs 1-3
```

### Redundant Link Design

Team1 and Team2 have two GigabitEthernet connections.

STP was used to prevent the redundant links from creating a Layer 2 loop.

At one point during testing, the topology contained a forwarding path and a redundant path:

```text
Team1 Gi0/1  <---->  Team2 Gi0/1
Team1 Gi0/2  <---->  Team2 Gi0/2
```

STP placed one path into a blocking state while keeping another path forwarding.

---

# 1. Initial Network

Initially, all nine PCs were placed in the same IPv4 network:

```text
192.168.1.0/24
```

The original workstation addressing was:

| PC | Initial IP Address |
|---|---|
| Sales1 | 192.168.1.11 |
| Marketing1 | 192.168.1.12 |
| Admin1 | 192.168.1.13 |
| Sales2 | 192.168.1.21 |
| Marketing2 | 192.168.1.22 |
| Admin2 | 192.168.1.23 |
| Sales3 | 192.168.1.31 |
| Marketing3 | 192.168.1.32 |
| Admin3 | 192.168.1.33 |

Subnet mask:

```text
255.255.255.0
```

No default gateway was required because the initial topology did not include a router.

Initial connectivity was verified successfully after the switches learned the required MAC addresses.

---

# 2. Switch Hostnames

The three switches were configured with descriptive hostnames:

| Switch | Hostname | Role |
|---|---|---|
| Team 1 | `Team1_Switch` | VTP Server |
| Team 2 | `Team2_Switch` | VTP Client |
| Team 3 | `Team3_Switch` | VTP Client |

Example:

```cisco
enable
configure terminal
hostname Team1_Switch
end
```

The same approach was used for Team2 and Team3.

---

# 3. Switching Loop Demonstration

A redundant connection was used to intentionally create a switching loop between Team1 and Team2.

The additional connection was:

```text
Team2 Gi0/1 <---- crossover cable ----> Team1 Gi0/1
```

With the redundant path in place and STP disabled, an ICMP test was performed in Cisco Packet Tracer Simulation Mode.

The simulation demonstrated the effects of the Layer 2 switching loop, including repeated/dropped traffic.

This provided a practical demonstration of why a loop-prevention mechanism is required in a switched Ethernet network.

---

# 4. MAC Address Table Cleanup

After the loop demonstration, the dynamic MAC address tables were cleared before continuing with the STP configuration.

Command used:

```cisco
enable
clear mac address-table dynamic
```

This allowed the switching behavior to be observed again without relying on stale dynamic MAC entries.

---

# 5. Spanning Tree Protocol

STP was enabled for VLAN 1 on all three switches.

```cisco
enable
configure terminal
spanning-tree vlan 1
end
copy running-config startup-config
```

The redundant Team1/Team2 connection was then restored.

## STP Verification

The following command was used:

```cisco
show spanning-tree
```

Team1 showed the redundant link in a blocked state:

```text
Gi0/1   Altn BLK
Gi0/2   Root FWD
```

This demonstrated that STP was preventing a Layer 2 loop while maintaining connectivity through the forwarding path.

---

# 6. STP Reconvergence

The forwarding Team1/Team2 link was temporarily removed to observe STP reconvergence.

After the forwarding link was removed, the previously blocked path transitioned into a forwarding state.

The resulting behavior demonstrated the purpose of STP redundancy:

```text
Normal state:

Primary path     -> Forwarding
Redundant path   -> Blocking

After primary path failure:

Primary path     -> Unavailable
Redundant path   -> Forwarding
```

The switch state was verified using:

```cisco
show spanning-tree
```

---

# 7. Trunk Configuration

The GigabitEthernet switch-to-switch links were configured as 802.1Q trunks.

The following configuration was applied to the GigabitEthernet interfaces:

```cisco
enable
configure terminal
interface range gigabitEthernet 0/1 - 2
switchport mode trunk
end
copy running-config startup-config
```

## Trunk Verification

The following command was used:

```cisco
show interfaces trunk
```

The switches reported:

```text
Encapsulation: 802.1q
Status:        trunking
```

The trunk links initially allowed VLANs:

```text
1-1005
```

---

# 8. VTP Configuration

VLAN Trunking Protocol (VTP) was used to distribute VLAN information from Team1 to Team2 and Team3.

## VTP Roles

| Switch | VTP Mode | Domain |
|---|---|---|
| Team1_Switch | Server | Provo |
| Team2_Switch | Client | Provo |
| Team3_Switch | Client | Provo |

The VTP configuration used the lab's specified domain and authentication settings.

> **Security note:** The VTP password used in the Packet Tracer lab is intentionally not reproduced in this public README.

## Team1 — VTP Server

```cisco
enable
configure terminal
vtp domain Provo
vtp mode server
vtp password <LAB_VTP_PASSWORD>
end
copy running-config startup-config
```

## Team2 — VTP Client

```cisco
enable
configure terminal
vtp domain Provo
vtp mode client
vtp password <LAB_VTP_PASSWORD>
end
copy running-config startup-config
```

## Team3 — VTP Client

```cisco
enable
configure terminal
vtp domain Provo
vtp mode client
vtp password <LAB_VTP_PASSWORD>
end
copy running-config startup-config
```

VTP status was verified using:

```cisco
show vtp status
```

Team3 successfully reported:

```text
VTP Version                     : 1
VTP Operating Mode              : Client
VTP Domain Name                 : Provo
```

The matching VTP configuration allowed VLAN information to propagate from Team1 to the client switches.

---

# 9. VLAN Design

Three department-based VLANs were created.

| VLAN ID | VLAN Name | Department | Access Ports |
|---:|---|---|---|
| 10 | Sales | Sales | Fa0/1–Fa0/4 |
| 20 | Marketing | Marketing | Fa0/5–Fa0/8 |
| 30 | Admin | Administration | Fa0/9–Fa0/12 |

The VLANs were created on the VTP Server:

```cisco
vlan 10
name Sales
exit

vlan 20
name Marketing
exit

vlan 30
name Admin
exit
```

The VLANs then appeared on the VTP Client switches.

---

# 10. Access Port Configuration

The department workstation ports were assigned to their corresponding VLANs.

## Sales — VLAN 10

```cisco
interface range fastEthernet 0/1 - 4
switchport mode access
switchport access vlan 10
exit
```

## Marketing — VLAN 20

```cisco
interface range fastEthernet 0/5 - 8
switchport mode access
switchport access vlan 20
exit
```

## Administration — VLAN 30

```cisco
interface range fastEthernet 0/9 - 12
switchport mode access
switchport access vlan 30
exit
```

These access-port assignments were configured on all three switches.

---

# 11. Final IP Addressing

After VLAN configuration, the PCs were assigned VLAN-specific IPv4 addresses.

| PC | VLAN | Final IP Address | Subnet Mask |
|---|---:|---|---|
| Sales1 | 10 | `192.168.1.10` | `255.255.255.0` |
| Sales2 | 10 | `192.168.1.20` | `255.255.255.0` |
| Sales3 | 10 | `192.168.1.30` | `255.255.255.0` |
| Marketing1 | 20 | `192.168.2.10` | `255.255.255.0` |
| Marketing2 | 20 | `192.168.2.20` | `255.255.255.0` |
| Marketing3 | 20 | `192.168.2.30` | `255.255.255.0` |
| Admin1 | 30 | `192.168.3.10` | `255.255.255.0` |
| Admin2 | 30 | `192.168.3.20` | `255.255.255.0` |
| Admin3 | 30 | `192.168.3.30` | `255.255.255.0` |

No default gateway was configured because this lab does not include a router or Layer 3 switch for inter-VLAN routing.

---

# 12. VLAN Verification

VLAN membership was verified using:

```cisco
show vlan brief
```

Example final access-port assignment:

```text
10   Sales       active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
20   Marketing   active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
30   Admin       active    Fa0/9, Fa0/10, Fa0/11, Fa0/12
```

The same VLAN structure was verified on Team1, Team2, and Team3.

---

# 13. Connectivity Verification

The final network behavior was tested using ICMP.

## Same-VLAN Connectivity

Sales1:

```text
192.168.1.10
```

was able to communicate with:

```text
Sales2 192.168.1.20
Sales3 192.168.1.30
```

### Sales1 → Sales2

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

### Sales1 → Sales3

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This verified that VLAN 10 traffic could travel between workstations connected to different switches.

---

# 14. Cross-VLAN Isolation

Because no router or Layer 3 switch was configured, the VLANs were not routed between one another.

## Sales1 → Marketing2

Source:

```text
192.168.1.10
```

Destination:

```text
192.168.2.20
```

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

## Sales1 → Admin2

Source:

```text
192.168.1.10
```

Destination:

```text
192.168.3.20
```

Result:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

These results demonstrated the expected separation between VLAN 10, VLAN 20, and VLAN 30.

---

# 15. Final Trunk Verification

Before the final VLAN restriction, Team1 was verified with:

```cisco
show interfaces trunk
```

The relevant state was:

```text
Gig0/1      trunking
Gig0/2      trunking
```

Both trunks initially allowed:

```text
1-1005
```

The active VLANs included:

```text
1,10,20,30
```

At that stage, STP was also controlling the redundant Team1/Team2 links, with one link forwarding and the other blocked.

---

# 16. VLAN 10 Trunk Restriction

The final configuration change removed VLAN 10 from both Team1 trunk interfaces.

```cisco
enable
configure terminal
interface range gigabitEthernet 0/1 - 2
switchport trunk allowed vlan remove 10
end
copy running-config startup-config
```

This restricts VLAN 10 from crossing the two specified Team1 trunk interfaces.

The configuration was then verified with:

```cisco
show interfaces trunk
```

The final lab state therefore included the deliberate restriction of VLAN 10 on Team1's two GigabitEthernet trunk interfaces.

---

# 17. Verification Commands Used

The following commands were important throughout the project.

### VLANs

```cisco
show vlan brief
```

### VTP

```cisco
show vtp status
```

### Trunks

```cisco
show interfaces trunk
```

### Spanning Tree

```cisco
show spanning-tree
```

### MAC Address Table

```cisco
show mac address-table
```

### Clear Dynamic MAC Entries

```cisco
clear mac address-table dynamic
```

### Save Configuration

```cisco
copy running-config startup-config
```

---

# 18. Troubleshooting Performed

Several issues and behaviors were encountered during the lab.

## Initial Ping Timeout

The first ping between hosts could time out while the switches learned MAC addresses.

Repeating the ping after MAC learning resulted in successful communication.

This highlighted the importance of allowing Layer 2 devices to populate their forwarding information.

## Switching Loop

An intentional redundant connection was used to demonstrate the effect of a Layer 2 loop.

The simulation showed problematic traffic behavior until the loop was removed.

## STP Blocking

After STP was enabled, one redundant link was placed into a blocking state.

This prevented the physical redundancy from becoming a logical switching loop.

## STP Reconvergence

When the forwarding link was removed, STP reconverged and allowed the previously blocked path to forward traffic.

## VTP Propagation

After VLANs were created on Team1, the VLANs appeared on Team2 and Team3 because the switches were operating as VTP Server/Clients within the same VTP domain.

## VLAN Access Port Assignment

VTP propagated the VLAN definitions, but the workstation-facing access ports still needed to be assigned to the appropriate VLANs locally on Team2 and Team3.

This was confirmed using:

```cisco
show vlan brief
```

---

# 19. Key Networking Concepts Demonstrated

## VLANs

VLANs divide a Layer 2 switch network into separate broadcast domains.

In this project:

```text
VLAN 10 = Sales
VLAN 20 = Marketing
VLAN 30 = Admin
```

## Trunking

Trunk links allow multiple VLANs to travel between switches over a shared physical connection.

The project used 802.1Q trunking on the GigabitEthernet switch-to-switch links.

## VTP

VTP was used to distribute VLAN information from Team1 to the VTP Client switches.

```text
Team1 = Server
Team2 = Client
Team3 = Client
```

## STP

STP prevents Layer 2 loops by calculating a loop-free forwarding topology and blocking redundant paths when necessary.

The project demonstrated both:

- Blocking of a redundant path
- Recovery/reconvergence after a forwarding path was removed

## Inter-VLAN Routing

No inter-VLAN routing was configured.

Therefore:

```text
VLAN 10 ↛ VLAN 20
VLAN 10 ↛ VLAN 30
VLAN 20 ↛ VLAN 30
```

Communication within the same VLAN was possible because the switches operated at Layer 2.

---

# 20. Skills Demonstrated

This project demonstrates practical experience with:

- Cisco IOS CLI
- Cisco Packet Tracer
- Cisco Catalyst 2960 switches
- VLAN creation
- VLAN naming
- Access-port configuration
- 802.1Q trunking
- VTP Server/Client configuration
- Spanning Tree Protocol
- STP verification
- Redundant switch links
- Layer 2 loop troubleshooting
- MAC address learning
- IPv4 addressing
- Subnet masks
- ICMP/Ping troubleshooting
- Network segmentation
- Layer 2 connectivity analysis
- Configuration verification
- Saving switch configurations

---

# 21. Project Evidence

The project included verification evidence for the major stages of the lab.

Suggested screenshots to retain with the Packet Tracer project include:

1. Initial successful ICMP exchange in Simulation Mode
2. ICMP behavior during the induced switching loop
3. `show spanning-tree` showing the blocked redundant port
4. `show spanning-tree` after reconvergence showing the forwarding port
5. Team3 `show interfaces trunk`
6. Team3 `show vlan brief`
7. Final ping results showing same-VLAN success and cross-VLAN failure
8. Team1 `show interfaces trunk` showing the VLAN 10 trunk restriction

If screenshots are stored in the repository, a suggested structure is:

```text
screenshots/
├── 01-initial-ping.png
├── 02-switching-loop.png
├── 03-stp-blocked-port.png
├── 04-stp-forwarding-port.png
├── 05-team3-trunk.png
├── 06-team3-vlans.png
├── 07-vlan-connectivity.png
└── 08-final-trunk-restriction.png
```

---

# 22. Project Files

A simple repository structure can be:

```text
cisco-vlan-trunking-lab/
├── README.md
├── packet-tracer/
│   └── VLAN-Trunking-STP-Lab.pkt
└── Evidence/
    ├── 01-initial-ping.png
    ├── 02-switching-loop.png
    ├── 03-stp-blocked-port.png
    ├── 04-stp-forwarding-port.png
    ├── 05-team3-trunk.png
    ├── 06-team3-vlans.png
    ├── 07-vlan-connectivity.png
    └── 08-final-trunk-restriction.png
```

Rename the `.pkt` file to the actual filename used in the repository.

---

# 23. Final Configuration Summary

| Component | Final Configuration |
|---|---|
| Switches | 3 × Cisco 2960 |
| VTP Server | Team1 |
| VTP Clients | Team2, Team3 |
| VTP Domain | Provo |
| VLAN 10 | Sales |
| VLAN 20 | Marketing |
| VLAN 30 | Admin |
| Trunking | 802.1Q |
| STP | Enabled for VLAN 1 |
| Routing | Not configured |
| VLAN 10 restriction | Removed from Team1 Gi0/1–2 trunk allowed lists |
| Sales subnet | 192.168.1.0/24 |
| Marketing subnet | 192.168.2.0/24 |
| Admin subnet | 192.168.3.0/24 |

---

# 24. Outcome

This project provided practical experience moving from a flat switched network to a segmented VLAN-based network.

The most important troubleshooting lesson was that successful VLAN configuration requires checking **multiple layers of the topology**:

```text
PC IP Configuration
        ↓
Access Port VLAN
        ↓
Switch VLAN Database
        ↓
Trunk Configuration
        ↓
VTP Propagation
        ↓
STP State
        ↓
End-to-End Connectivity
```

Using commands such as:

```cisco
show vlan brief
show interfaces trunk
show vtp status
show spanning-tree
```

made it possible to verify each part of the configuration rather than assuming that a configuration change had worked.

---

## Conclusion

The completed Packet Tracer project demonstrates a three-switch VLAN environment with Sales, Marketing, and Administration network segments.

The lab successfully demonstrated:

- Layer 2 VLAN segmentation
- Inter-switch trunking
- VTP-based VLAN propagation
- STP loop prevention
- STP reconvergence
- Same-VLAN communication across switches
- Cross-VLAN isolation without routing
- Selective VLAN restriction on trunk links

The project provides a practical example of how VLANs, trunking, VTP, and STP work together to build and troubleshoot a segmented switched network.
