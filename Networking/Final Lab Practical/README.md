# IT 350 – Final Lab Practical

## Project Overview

This project documents the Final Lab Practical for the Network Configuration and Design.

The practical brings together multiple Cisco networking concepts in a simulated enterprise network. It involves configuring routers and switches, implementing VLAN segmentation, establishing trunk links, providing inter-VLAN routing and DHCP services, configuring NAT/PAT, implementing routing protocols, and verifying redundancy and Layer 2 network behaviour.

The project is completed in Cisco Packet Tracer and focuses on applying networking concepts through device configuration, command-line verification, and structured troubleshooting.

## Objectives

The objectives of this practical are to:

- Configure Cisco router and switch hostnames, interfaces, and IP addressing.
- Apply device access security and remote management settings.
- Configure VLANs and assign switch ports appropriately.
- Configure trunk links to carry traffic for the required VLANs.
- Implement router-on-a-stick for inter-VLAN routing.
- Configure DHCP to provide IP addressing to client devices.
- Implement NAT/PAT for communication with external networks.
- Configure and verify routing, including OSPF.
- Establish and verify EtherChannel links.
- Configure Spanning Tree Protocol (STP) priorities as required.
- Verify network connectivity and troubleshoot configuration issues.
- Save device configurations and collect evidence of the completed work.

## Technologies and Tools

| Technology or tool | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation and testing |
| Cisco IOS CLI | Router and switch configuration |
| IPv4 addressing | Device addressing and subnetting |
| VLANs | Logical network segmentation |
| IEEE 802.1Q trunking | Carrying multiple VLANs over a link |
| Router-on-a-stick | Inter-VLAN routing |
| DHCP | Automatic client IP configuration |
| NAT/PAT | Translating private addresses for external connectivity |
| OSPF | Dynamic routing |
| EtherChannel | Combining physical links into a logical link |
| STP | Preventing Layer 2 switching loops |
| Ping and verification commands | Connectivity testing and troubleshooting |

## Network Topology

The practical uses a multi-device Cisco network containing routers, switches, and end devices. The configuration includes a site router identified as `R2-SITE2`, switching devices identified as `S1`, `S2`, and `S3`, and a provider-facing connection.

The exact topology, device roles, and addressing must be taken from the original Packet Tracer activity.


## Practical Requirements

The final practical covers the following configuration areas.

### 1. Device Hostnames and Initial Configuration

Configure the required router and switch hostnames and verify that the devices are identified correctly.

### 2. Interface Status and IP Addressing

Configure the required interface addresses and ensure the interfaces are operational. Verify addressing and interface status using:

```text
show ip interface brief
```

### 3. Device Security and Remote Access

Apply the required passwords, console and privileged access settings, and remote access configuration. Verify the settings against the practical instructions.

### 4. VLAN Configuration

Create and configure the required VLANs, including VLAN 20 where specified. Assign access ports to the appropriate VLANs.

Useful verification command:

```text
show vlan brief
```

### 5. Trunk Configuration

Configure the required switch links as trunks and verify the allowed VLANs.

```text
show interfaces trunk
```

### 6. Router-on-a-Stick

Configure router subinterfaces and 802.1Q encapsulation as required to route traffic between VLANs.

Verify the interface configuration and connectivity between the relevant VLANs.

### 7. DHCP Configuration

Configure DHCP pools and associated addressing options for the required client networks. Confirm that client devices receive the expected IP configuration.

### 8. NAT/PAT Configuration

Configure the required NAT/PAT rules and identify the inside and outside interfaces. Verify translations and connectivity to the external network where applicable.

Useful verification commands:

```text
show ip nat translations
show ip nat statistics
```

### 9. Routing Configuration

Configure the routing settings required by the topology, including static or default routes where specified. Verify that the routing table contains the necessary routes.

```text
show ip route
```

### 10. OSPF Configuration

Configure OSPF on the designated router interfaces and advertise the required networks.

During the practical work, OSPF on `R2-SITE2` was checked. The router had the following recorded interface information:

| Interface | Recorded address | Purpose |
|---|---|---|
| GigabitEthernet0/0 | `216.127.251.10/30` | Provider-facing connection |
| GigabitEthernet0/1 | `192.168.1.1/24` | LAN interface |

The OSPF neighbour relationship with the provider at `216.127.251.9` was verified, and the LAN interface was configured as passive.

These details document the observed configuration and should be retained only if they match the final saved Packet Tracer file.

Useful verification commands:

```text
show ip ospf neighbor
show ip protocols
show ip route
```

### 11. EtherChannel Configuration

Configure the required EtherChannel links between switches and verify that the physical member interfaces have joined their logical port-channels.

During verification, the following was observed:

- `Po1` connected `S1` and `S2`.
- `Po2` connected `S1` and `S3`.
- The port-channel member interfaces displayed `(P)`, indicating that they were bundled in the port-channel.
- The port-channel interfaces were configured as trunks carrying VLANs 1 and 20.

Useful verification commands:

```text
show etherchannel summary
show interfaces trunk
```

The saved configuration should be checked to confirm that the intended channel-group settings and physical interfaces are present.

### 12. Spanning Tree Protocol

Configure STP priorities for the required VLANs and switches according to the lab instructions. Verify the spanning-tree root bridge and port roles.

Useful verification commands:

```text
show spanning-tree
show spanning-tree vlan 1
show spanning-tree vlan 20
```

The final root bridge selection and STP priority configuration must be verified from the saved lab.

### 13. Save Configurations and Collect Evidence

Save the Packet Tracer project and device configurations as required. Capture screenshots showing the relevant configurations and successful verification results.

The evidence should accurately represent the final saved state of the practical.

## Configuration and Verification Summary

The following table distinguishes work that was verified during troubleshooting from requirements that still need confirmation in the final submission.

| Area | Recorded status |
|---|---|
| OSPF neighbour relationship on `R2-SITE2` | Verified during troubleshooting |
| Passive LAN interface on `R2-SITE2` | Verified during troubleshooting |
| EtherChannel `Po1` between `S1` and `S2` | Member links verified as bundled |
| EtherChannel `Po2` between `S1` and `S3` | Member links verified as bundled |
| Port-channel trunk VLANs 1 and 20 | Observed during verification |
| Default route | Observed during verification |
| Hostnames and all interface addressing | Final state requires confirmation |
| Device security and remote access | Final state requires confirmation |
| VLAN assignments and trunk configuration across all links | Final state requires confirmation |
| Router-on-a-stick | Final state requires confirmation |
| DHCP services and client leases | Final state requires confirmation |
| NAT/PAT and external connectivity | Final state requires confirmation |
| STP priorities and root bridge selection | Final state requires confirmation |
| Final saved project and evidence screenshots | Must be checked before submission |

This status table is intended to support accurate documentation. A requirement should be marked complete only after it has been verified in the final saved project.

## Testing and Validation

Testing should cover both individual configuration requirements and end-to-end connectivity.

| Test | Verification method | Expected result |
|---|---|---|
| Interface addressing | `show ip interface brief` | Correct addresses and operational interfaces |
| VLAN membership | `show vlan brief` | Access ports assigned to the required VLANs |
| Trunk links | `show interfaces trunk` | Required trunks and VLANs present |
| Inter-VLAN routing | Ping between permitted VLAN hosts | Connectivity where routing and policies permit |
| DHCP | Inspect client IP settings | Correct address, mask, gateway, and DNS settings as required |
| NAT/PAT | Inspect translations and test external reachability | Expected translation and connectivity |
| OSPF | `show ip ospf neighbor` | Expected neighbour relationship established |
| Routing table | `show ip route` | Required connected and learned routes present |
| EtherChannel | `show etherchannel summary` | Intended member links bundled |
| STP | `show spanning-tree vlan 1` and VLAN 20 | Expected root bridge and port roles |
| Remote access | Connect using the required management method | Access succeeds under the configured policy |

Record the actual results of each test before publishing the final README. Do not describe connectivity as successful unless it has been tested.

## Troubleshooting Approach

The practical reinforced a structured approach to network troubleshooting:

1. Check physical and logical interface status.
2. Confirm IP addresses and subnet masks.
3. Verify VLAN membership and trunk configuration.
4. Check routing tables and OSPF neighbour relationships.
5. Inspect EtherChannel member status and port-channel configuration.
6. Verify NAT/PAT rules and translations where relevant.
7. Test connectivity progressively, starting with the local network and moving toward remote destinations.
8. Save the corrected configuration and capture evidence of the final state.

Useful general verification commands include:

```text
show running-config
show ip interface brief
show ip route
show ip protocols
show ip ospf neighbor
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree
```

Use commands appropriate to the device and the feature being investigated.

## Skills Demonstrated

This project provides practical experience in:

- Cisco IOS router and switch configuration.
- IPv4 addressing and routing.
- VLAN segmentation and trunking.
- Inter-VLAN routing.
- DHCP and NAT/PAT concepts.
- OSPF configuration and verification.
- EtherChannel configuration and troubleshooting.
- STP verification and Layer 2 loop prevention.
- Network connectivity testing.
- Configuration validation and technical documentation.

## Repository Structure

Organise the project files as follows:

```text
IT350-Final-Lab-Practical/
├── README.md
├── packet-tracer/
│   └── final-lab-practical.pkt
└── images/
    ├── final-topology.png
    ├── interface-status.png
    ├── vlan-and-trunk-verification.png
    ├── ospf-neighbor.png
    ├── etherchannel-summary.png
    ├── nat-verification.png
    └── spanning-tree.png
```

This is a suggested structure. Include only files and screenshots that actually exist and correspond to the final project. Rename the Packet Tracer file to match its real filename.

## How to View the Project

1. Clone or download this repository.
2. Open Cisco Packet Tracer.
3. Open the `.pkt` file in the `packet-tracer/` directory.
4. Inspect the network topology and device configurations.
5. Use the verification commands documented above to inspect the relevant features.

A compatible version of Cisco Packet Tracer may be required to open the project.

## Conclusion

The Final Lab Practical brings together key networking technologies in a single simulated environment. It provides an opportunity to apply routing, switching, network segmentation, address translation, redundancy, and verification techniques in a practical setting.

The work documented here includes verified OSPF and EtherChannel observations, alongside the remaining configuration areas that must be confirmed against the final saved project. This distinction keeps the repository technically accurate and makes the README a useful record of the practical work.