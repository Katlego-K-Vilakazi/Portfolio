# Configuring InterVLAN Routing

## Overview

This Cisco Packet Tracer project integrates the Team Rooms with the Provo network and provides interVLAN routing and Internet access.

The main tasks included:

* Identifying the existing STP Root Bridge
* Connecting Team1 to ProvoDist using a four-link PAgP EtherChannel
* Connecting ProvoDist to the Provo router
* Configuring router-on-a-stick interVLAN routing
* Adding the required VLANs to ProvoDist
* Updating RIP to advertise the Team networks
* Troubleshooting DNS resolution
* Configuring VLAN 1 management addresses
* Configuring DHCP for the management network
* Enabling Telnet remote management on Team3
* Verifying connectivity throughout the topology

**Platform:** Cisco Packet Tracer
**Project:** Configuring InterVLAN Routing

---

## Network Design

### VLANs

| VLAN | Name       | Ports | Network          | Default Gateway |
| ---: | ---------- | ----- | ---------------- | --------------- |
|    1 | Management | 13–16 | `192.168.4.0/24` | `192.168.4.1`   |
|   10 | Sales      | 1–4   | `192.168.1.0/24` | `192.168.1.1`   |
|   20 | Marketing  | 5–8   | `192.168.2.0/24` | `192.168.2.1`   |
|   30 | Admin      | 9–12  | `192.168.3.0/24` | `192.168.3.1`   |

### Switch Management Addresses

| Device    | VLAN 1 Address   | Default Gateway |
| --------- | ---------------- | --------------- |
| Team1     | `192.168.4.2/24` | `192.168.4.1`   |
| Team2     | `192.168.4.3/24` | `192.168.4.1`   |
| Team3     | `192.168.4.4/24` | `192.168.4.1`   |
| ProvoDist | `192.168.4.5/24` | `192.168.4.1`   |

### Router-on-a-Stick

The Provo router uses `GigabitEthernet0/0` for the router-on-a-stick configuration.

| Interface | VLAN | Purpose                  | IP Address       |
| --------- | ---: | ------------------------ | ---------------- |
| `Gi0/0`   |    — | Physical interface       | No IP            |
| `Gi0/0.1` |   10 | Sales                    | `192.168.1.1/24` |
| `Gi0/0.2` |   20 | Marketing                | `192.168.2.1/24` |
| `Gi0/0.3` |   30 | Admin                    | `192.168.3.1/24` |
| `Gi0/0.4` |    1 | Management / Native VLAN | `192.168.4.1/24` |

The worksheet specifies VLAN 1 as the native VLAN on the management subinterface.

---

## Initial Connectivity Verification

The first test was performed from Sales1.

Sales1 was able to communicate with PCs on the other Team switches before the interVLAN routing configuration was completed.

This established a working baseline for the existing VLAN configuration.

The Provo Network Admin PC was also able to access:

```text
http://www.ralph.com
http://www.company.internal
```

before the new routing work began.

---

## STP Root Bridge

The existing Root Bridge was identified before making changes to the topology.

### Root Bridge

**Team3**

```text
Priority: 8193
MAC Address: 0001.4385.CE06
```

Team3 reported itself as the Root Bridge, while Team1 and Team2 identified Team3's bridge ID as their Root ID.

This was important because adding the four parallel links between Team1 and ProvoDist initially caused STP to block redundant paths.

---

## EtherChannel Between Team1 and ProvoDist

The worksheet requires four links between Team1 and ProvoDist using ports 21–24 on both switches. The links were configured as trunks before being bundled into an EtherChannel.

### Team1 Configuration

```cisco
enable
configure terminal
interface range fastEthernet 0/21 - 24
switchport mode trunk
channel-protocol pagp
channel-group 1 mode desirable
end
```

### ProvoDist Configuration

```cisco
enable
configure terminal
interface range fastEthernet 0/21 - 24
switchport mode trunk
channel-protocol pagp
channel-group 1 mode desirable
end
```

### Verification

```cisco
show etherchannel summary
show interfaces trunk
```

The completed configuration showed:

```text
Po1(SU) PAgP
```

with all four physical interfaces participating in the port-channel.

The EtherChannel also operated as a trunk carrying the required VLANs.

---

## Provo Router Connection

ProvoDist was connected to the Provo router using a GigabitEthernet connection.

The physical router interface was verified as operational before configuring the router-on-a-stick subinterfaces.

---

## Router-on-a-Stick Configuration

The Provo router was configured with four subinterfaces.

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
no shutdown
exit

interface gigabitEthernet 0/0.1
encapsulation dot1Q 10
ip address 192.168.1.1 255.255.255.0
exit

interface gigabitEthernet 0/0.2
encapsulation dot1Q 20
ip address 192.168.2.1 255.255.255.0
exit

interface gigabitEthernet 0/0.3
encapsulation dot1Q 30
ip address 192.168.3.1 255.255.255.0
exit

interface gigabitEthernet 0/0.4
encapsulation dot1Q 1 native
ip address 192.168.4.1 255.255.255.0
exit

end
```

### Verification

```cisco
show ip interface brief
show running-config
```

The four subinterfaces were verified as operational.

The worksheet requires the following subinterface assignments.

---

## VLAN Troubleshooting

After the router subinterfaces and PC default gateways were configured, the gateway ping initially failed.

The issue was investigated by comparing the VLAN databases on Team1 and ProvoDist.

Team1 contained:

```text
VLAN 10 Sales
VLAN 20 Marketing
VLAN 30 Admin
```

ProvoDist initially contained only VLAN 1.

### VLANs Added to ProvoDist

```cisco
enable
configure terminal

vlan 10
name Sales
exit

vlan 20
name Marketing
exit

vlan 30
name Admin
exit

end
```

Verification:

```cisco
show vlan brief
```

The required VLANs were then visible and active on ProvoDist.

The ProvoDist interface connected to the Provo router was also configured as a trunk so that the router-on-a-stick subinterfaces could carry the VLAN traffic:

```cisco
enable
configure terminal
interface gigabitEthernet 0/1
switchport mode trunk
end
```

After these changes, Admin3 successfully reached its default gateway:

```text
192.168.3.1
```

with:

```text
4 packets sent
4 packets received
0% packet loss
```

This troubleshooting sequence follows the worksheet's instruction to compare the VLAN databases and identify the missing VLANs.

---

## RIP Routing

The Provo router was already using RIP version 2 for the existing Provo/ISP network.

The original routing configuration included:

```cisco
router rip
version 2
network 172.16.0.0
network 198.3.24.0
```

The new Team networks were added:

```cisco
router rip
version 2
network 172.16.0.0
network 192.168.1.0
network 192.168.2.0
network 192.168.3.0
network 192.168.4.0
network 198.3.24.0
```

### Verification

On the Provo router:

```cisco
show running-config
```

The routing configuration showed all of the required Team networks.

On the ISP router:

```cisco
show ip route
```

The ISP learned the Team networks through RIP, including:

```text
192.168.1.0/24
192.168.2.0/24
192.168.3.0/24
192.168.4.0/24
```

with the Provo router as the next hop.

The worksheet requires the newly added networks to be added to the routing protocol and verified on the ISP router.

---

## Internet Connectivity

Marketing2 initially failed to access:

```text
198.168.5.2
```

This was expected before the new Team networks were advertised through RIP.

Marketing2 was configured with:

```text
IP Address:       192.168.2.20
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.2.1
```

After the routing changes, the following tests succeeded:

```text
ping 192.168.2.1
ping 198.3.24.4
ping 198.168.5.2
```

The browser then successfully opened:

```text
http://198.168.5.2
```

and displayed:

```text
Welcome to the Internet
```

---

## DNS Troubleshooting

Sales3 initially failed to resolve:

```text
www.ralph.com
```

The problem was traced to the DNS configuration.

### Sales3 Final Configuration

```text
IP Address:       192.168.1.30
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.1.1
DNS Server:       172.16.0.6
```

After configuring the DNS server, Sales3 successfully accessed:

```text
http://www.ralph.com
```

Result:

```text
Welcome to the Internet
```

Sales3 also successfully accessed:

```text
http://www.company.internal
```

Result:

```text
Welcome to the Company
```

This completed the DNS troubleshooting requirement in the project.

---

## VLAN 1 Management

VLAN 1 was used as the management network.

### Team1

```cisco
enable
configure terminal
interface vlan 1
ip address 192.168.4.2 255.255.255.0
no shutdown
exit
ip default-gateway 192.168.4.1
end
```

### Team2

```cisco
enable
configure terminal
interface vlan 1
ip address 192.168.4.3 255.255.255.0
no shutdown
exit
ip default-gateway 192.168.4.1
end
```

### Team3

```cisco
enable
configure terminal
interface vlan 1
ip address 192.168.4.4 255.255.255.0
no shutdown
exit
ip default-gateway 192.168.4.1
end
```

### ProvoDist

```cisco
enable
configure terminal
interface vlan 1
ip address 192.168.4.5 255.255.255.0
no shutdown
exit
ip default-gateway 192.168.4.1
end
```

Each switch was verified with:

```cisco
show ip interface brief
```

and VLAN 1 showed an `up/up` status.

The worksheet specifies management addresses from `192.168.4.2` through `192.168.4.5`.

---

## DHCP Configuration

The Provo router was configured to provide DHCP for:

```text
192.168.4.0/24
```

The static addresses used by the router and switches were excluded:

```cisco
ip dhcp excluded-address 192.168.4.1 192.168.4.5
```

### DHCP Pool

```cisco
ip dhcp pool MANAGEMENT
network 192.168.4.0 255.255.255.0
default-router 192.168.4.1
dns-server 172.16.0.6
```

### Verification

```cisco
show running-config | section dhcp
```

The DHCP configuration was confirmed.

---

## VLAN 1 Access Ports

Ports 13–16 were assigned to VLAN 1 on each switch.

The configuration used was:

```cisco
enable
configure terminal
interface range fastEthernet 0/13 - 16
switchport mode access
switchport access vlan 1
end
```

Verification:

```cisco
show vlan brief
```

The following switches were verified:

* Team1
* Team2
* Team3
* ProvoDist

The worksheet requires ports 13–16 to be on VLAN 1 for the DHCP test PC.

---

## DHCP Test

A test PC was connected to a Team Room port between 13 and 16 and configured for DHCP.

The PC successfully received:

```text
IPv4 Address:      192.168.4.6
Subnet Mask:       255.255.255.0
Default Gateway:   192.168.4.1
```

This confirmed that:

* The DHCP pool was active.
* VLAN 1 traffic could reach the router.
* The DHCP server could allocate an address.
* The excluded addresses were respected.

---

## Telnet Remote Management

The Provo Network Admin PC was used to test remote access to Team3.

Team3's management address was:

```text
192.168.4.4
```

### Connectivity Test

```text
ping 192.168.4.4
```

Result:

```text
Sent = 4
Received = 4
Lost = 0 (0% loss)
```

This confirmed that Team3 was reachable over the management network.

### Team3 VTY Configuration

```cisco
enable
configure terminal
line vty 0 4
password cisco
login
transport input telnet
exit
end
```

Verification:

```cisco
show running-config | section line vty
```

The configuration showed:

```text
line vty 0 4
 password cisco
 login
 transport input telnet
```

An enable secret was also configured so the remote session could enter privileged EXEC mode:

```cisco
enable
configure terminal
enable secret class
end
```

### Telnet Test

From Provo Network Admin:

```text
telnet 192.168.4.4
```

The connection successfully opened and displayed the Team3 switch prompt.

This completed the remote-management requirement.

---

## Verification Summary

| Requirement                     | Result          |
| ------------------------------- | --------------- |
| Sales1 baseline connectivity    | ✅               |
| Provo Network Admin web access  | ✅               |
| STP Root Bridge identified      | ✅ Team3         |
| Team1–ProvoDist EtherChannel    | ✅ PAgP          |
| Router-on-a-stick subinterfaces | ✅               |
| VLANs added to ProvoDist        | ✅               |
| Admin3 gateway connectivity     | ✅               |
| RIP updated with Team networks  | ✅               |
| ISP learned Team routes         | ✅               |
| Marketing2 Internet access      | ✅               |
| Sales3 DNS resolution           | ✅               |
| `www.ralph.com`                 | ✅               |
| `www.company.internal`          | ✅               |
| Switch VLAN 1 management        | ✅               |
| DHCP management network         | ✅               |
| DHCP test PC                    | ✅ `192.168.4.6` |
| Team3 management reachability   | ✅               |
| Telnet remote management        | ✅               |

---

## Troubleshooting Lessons

### VLANs must exist on the switch

Configuring a router subinterface for a VLAN does not create that VLAN in the switch's VLAN database.

The gateway initially failed because ProvoDist did not have VLANs 10, 20, and 30.

### Trunks must carry the required VLANs

The ProvoDist-to-Provo router link needed to operate as a trunk because the router was using multiple 802.1Q subinterfaces.

### EtherChannel reduces the effect of STP blocking parallel links

Four parallel links initially caused STP to block redundant paths.

PAgP EtherChannel combined the physical links into one logical connection.

### Routing protocols need the new networks

The Provo router knew about the Team networks as directly connected networks, but the ISP did not initially have routes back to them.

Adding the networks to RIP allowed the ISP to learn them.

### DNS is separate from IP connectivity

Sales3 could have network connectivity while still failing to resolve a hostname.

Configuring the correct DNS server (`172.16.0.6`) resolved the hostname-access problem.

### Remote management requires both connectivity and VTY configuration

Team3 first had to be reachable at `192.168.4.4`.

The VTY lines then needed Telnet and authentication configuration.

An enable secret was also required to enter privileged EXEC mode through the remote session.

---

## Verification Commands

The following commands were used throughout the project:

```cisco
show vlan brief
show interfaces trunk
show etherchannel
show etherchannel summary
show ip interface brief
show running-config
show running-config | section dhcp
show running-config | section line vty
show ip route
```

End-device commands included:

```text
ipconfig
ipconfig /all
ping <destination>
telnet <management-ip>
```

---

## Skills Demonstrated

This project provided hands-on practice with:

* Cisco IOS
* Cisco Packet Tracer
* VLAN configuration
* 802.1Q trunking
* InterVLAN routing
* Router-on-a-stick
* PAgP EtherChannel
* Spanning Tree Protocol
* IPv4 addressing
* Default gateways
* RIP version 2
* Routing-table verification
* DHCP
* DNS troubleshooting
* Switch management interfaces
* VTY configuration
* Telnet
* Layer 2 troubleshooting
* Layer 3 troubleshooting
* End-to-end connectivity testing

---

## Project Outcome

The completed topology provides communication between the Team Room VLANs and the Provo network, Internet access through the ISP connection, DNS-based access to the required websites, DHCP for the VLAN 1 management network, and remote management access to Team3.

The project was completed through configuration, verification, and troubleshooting rather than relying only on initial configuration.
