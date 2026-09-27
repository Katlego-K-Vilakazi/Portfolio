# DHCP, DNS, and Switch Configuration

This project focuses on configuring and verifying **DHCP, DNS, switch management, routing, and end-to-end network connectivity** in a multi-router Cisco Packet Tracer topology.

---

## Project Overview

The purpose of this project was to configure a functioning network in Cisco Packet Tracer by:

* Enabling required router interfaces
* Configuring static routing between network segments
* Creating DHCP pools
* Excluding statically assigned addresses from DHCP
* Configuring DHCP relay with `ip helper-address`
* Configuring switch management IP addresses
* Configuring switch default gateways
* Creating DNS records
* Configuring routers and switches to use the DNS server
* Testing DNS name resolution
* Verifying end-to-end connectivity
* Troubleshooting network connectivity and DHCP/DNS issues

The project was completed progressively, testing each major component before moving to the next.

---

## Technologies and Concepts

* Cisco Packet Tracer
* Cisco IOS
* IPv4
* Static Routing
* DHCP
* DHCP Relay
* DNS
* Layer 2 Switching
* VLAN 1 Management SVI
* Default Gateways
* ICMP / `ping`
* ARP
* Network Troubleshooting
* Cisco IOS verification commands

---

# Network Topology

The network contains five routers and four switches serving separate IPv4 networks.

### Routers

| Device       | Role                                      |
| ------------ | ----------------------------------------- |
| `R1`         | Internal router                           |
| `R2_DHCP`    | DHCP router/server                        |
| `R3`         | Internal router and Admin network gateway |
| `ISP_Router` | Connection toward simulated Internet      |
| `Router`     | Simulated Internet-side router            |

### Switches

| Switch            | Network          | Management IP |
| ----------------- | ---------------- | ------------: |
| Sales Switch      | `192.168.1.0/24` | `192.168.1.2` |
| Marketing Switch  | `192.168.2.0/24` | `192.168.2.2` |
| Accounting Switch | `192.168.3.0/24` | `192.168.3.2` |
| Admin Switch      | `192.168.4.0/24` | `192.168.4.2` |

> The Packet Tracer switches retain the hostname `Switch`. The labels above describe their network/function in this project and are used to distinguish them in the documentation.

---

# IP Addressing

## LAN Networks

| Network          | Purpose            |       Gateway |
| ---------------- | ------------------ | ------------: |
| `192.168.1.0/24` | Sales              | `192.168.1.1` |
| `192.168.2.0/24` | Marketing          | `192.168.2.1` |
| `192.168.3.0/24` | Accounting         | `192.168.3.1` |
| `192.168.4.0/24` | Administration     | `192.168.4.1` |
| `192.168.5.0/24` | Simulated Internet | `192.168.5.1` |

## Router-to-Router Networks

| Network           | Device/Connection                 |
| ----------------- | --------------------------------- |
| `192.168.99.0/30` | R1 ↔ R2_DHCP                      |
| `192.168.99.4/30` | R2_DHCP ↔ R3                      |
| `192.168.99.8/30` | R3 ↔ ISP_Router                   |
| `198.3.24.0/24`   | ISP_Router ↔ Internet-side Router |

### Router Addresses

| Device     | Interface |      IP Address |
| ---------- | --------- | --------------: |
| R1         | Gi0/0     |  `192.168.99.1` |
| R1         | Gi0/1     |   `192.168.2.1` |
| R1         | Gi0/2     |   `192.168.1.1` |
| R2_DHCP    | Gi0/0     |  `192.168.99.5` |
| R2_DHCP    | Gi0/1     |  `192.168.99.2` |
| R2_DHCP    | Gi0/2     |   `192.168.3.1` |
| R3         | Gi0/0     |  `192.168.99.9` |
| R3         | Gi0/1     |  `192.168.99.6` |
| R3         | Gi0/2     |   `192.168.4.1` |
| ISP_Router | Gi0/1     | `192.168.99.10` |
| ISP_Router | S0/0/0    |    `198.3.24.3` |
| Router     | Fa0/0     |   `192.168.5.1` |
| Router     | S2/0      |    `198.3.24.4` |

---

# 1. Enable R3 Interfaces

R3 initially had all required interfaces administratively shut down.

The following interfaces were enabled:

```cisco
enable
configure terminal

interface GigabitEthernet0/0
 no shutdown
 exit

interface GigabitEthernet0/1
 no shutdown
 exit

interface GigabitEthernet0/2
 no shutdown
 exit

end
```

### Verification

```cisco
show ip interface brief
```

The required R3 interfaces were verified as:

```text
GigabitEthernet0/0    192.168.99.9    up    up
GigabitEthernet0/1    192.168.99.6    up    up
GigabitEthernet0/2    192.168.4.1      up    up
```

---

# 2. Static Routing

Static routes were configured on all five routers to provide connectivity between the different network segments.

The project allowed working routing configurations and recommended considering default routes where appropriate.

## R1

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.99.2
ip route 192.168.4.0 255.255.255.0 192.168.99.2
ip route 192.168.5.0 255.255.255.0 192.168.99.2
ip route 192.168.99.4 255.255.255.252 192.168.99.2
ip route 192.168.99.8 255.255.255.252 192.168.99.2
```

## R2_DHCP

The existing Sales route was retained:

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.99.1
```

Additional routes:

```cisco
ip route 192.168.2.0 255.255.255.0 192.168.99.1
ip route 192.168.4.0 255.255.255.0 192.168.99.6
ip route 192.168.5.0 255.255.255.0 192.168.99.6
ip route 192.168.99.8 255.255.255.252 192.168.99.6
```

## R3

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.99.5
ip route 192.168.2.0 255.255.255.0 192.168.99.5
ip route 192.168.3.0 255.255.255.0 192.168.99.5
ip route 192.168.5.0 255.255.255.0 192.168.99.10
ip route 192.168.99.0 255.255.255.252 192.168.99.5
```

## ISP_Router

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.99.9
ip route 192.168.2.0 255.255.255.0 192.168.99.9
ip route 192.168.3.0 255.255.255.0 192.168.99.9
ip route 192.168.99.0 255.255.255.252 192.168.99.9
ip route 192.168.99.4 255.255.255.252 192.168.99.9
```

An existing route toward the simulated Internet network was also present.

## Internet-side Router

```cisco
ip route 192.168.1.0 255.255.255.0 198.3.24.3
ip route 192.168.2.0 255.255.255.0 198.3.24.3
ip route 192.168.3.0 255.255.255.0 198.3.24.3
ip route 192.168.4.0 255.255.255.0 198.3.24.3
ip route 192.168.99.0 255.255.255.0 198.3.24.3
```

---

# 3. DHCP Configuration

DHCP was configured on `R2_DHCP`.

The existing Sales pool was used as the pattern for the additional networks.

## Sales

```cisco
ip dhcp excluded-address 192.168.1.1
ip dhcp excluded-address 192.168.1.2

ip dhcp pool Sales
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 192.168.4.3
```

## Marketing

```cisco
ip dhcp excluded-address 192.168.2.1
ip dhcp excluded-address 192.168.2.2

ip dhcp pool Marketing
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 192.168.4.3
```

## Accounting

```cisco
ip dhcp excluded-address 192.168.3.1
ip dhcp excluded-address 192.168.3.2

ip dhcp pool Accounting
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 192.168.4.3
```

## Administration

```cisco
ip dhcp excluded-address 192.168.4.1
ip dhcp excluded-address 192.168.4.2

ip dhcp pool Admin
 network 192.168.4.0 255.255.255.0
 default-router 192.168.4.1
 dns-server 192.168.4.3
```

### DHCP Verification

```cisco
show ip dhcp pool
```

The four DHCP pools were successfully displayed.

---

# 4. DHCP Relay

DHCP relay was required for networks where the DHCP server was not directly connected to the client network.

## Marketing

On R1's Marketing interface:

```cisco
interface GigabitEthernet0/1
 ip helper-address 192.168.3.1
```

## Administration

On R3's Admin interface:

```cisco
interface GigabitEthernet0/2
 ip helper-address 192.168.3.1
```

The Accounting network is directly connected to `R2_DHCP`, so a DHCP relay was not required there.

The existing Sales helper configuration was retained.

---

# 5. DHCP Client Verification

PC2 was tested using DHCP.

The resulting configuration was:

```text
IPv4 Address:     192.168.3.3
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.3.1
DHCP Server:      192.168.3.1
DNS Server:       192.168.4.3
```

This confirmed that the Accounting DHCP pool was operating correctly.

---

# 6. Switch Management Configuration

Each switch was configured with a management address on VLAN 1.

## Sales Switch

```cisco
interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.1.1
```

## Marketing Switch

```cisco
interface vlan 1
 ip address 192.168.2.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.2.1
```

## Accounting Switch

```cisco
interface vlan 1
 ip address 192.168.3.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.3.1
```

## Admin Switch

```cisco
interface vlan 1
 ip address 192.168.4.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.4.1
```

### Verification

Each switch was verified with:

```cisco
show ip interface brief
```

The VLAN 1 interface was confirmed as:

```text
up    up
```

and the expected management IP was displayed.

---

# 7. Switch Connectivity Test

The Accounting switch was used to test connectivity to the Sales switch.

Command:

```cisco
ping 192.168.1.2
```

Result:

```text
!!!!!
Success rate is 100 percent (5/5)
```

This verified communication between the switch management networks.

---

# 8. DNS Configuration

The DNS server is located at:

```text
192.168.4.3
```

The DNS service was enabled in Cisco Packet Tracer.

The project required DNS records for the network devices and an entry for the simulated Internet.

The following records were configured during the project:

| Name            | Type |         Address |
| --------------- | ---- | --------------: |
| `R1`            | A    |  `192.168.99.1` |
| `R2_DHCP`       | A    |  `192.168.99.2` |
| `R3`            | A    |  `192.168.99.9` |
| `ISP_Router`    | A    | `192.168.99.10` |
| `Router`        | A    |   `192.168.5.1` |
| `www.ralph.com` | A    |   `192.168.5.2` |

### DNS Configuration Location

In Packet Tracer:

```text
DNS Server
    └── Services
        └── DNS
```

The records were entered as **A Records**.

---

# 9. Client DNS Test

PC0 was used to test access to the simulated Internet website.

The browser address was:

```text
www.ralph.com
```

The webpage successfully loaded after the network and DNS configuration was completed.

This confirmed that:

1. PC0 had network connectivity.
2. PC0 could reach the DNS service.
3. `www.ralph.com` could be resolved.
4. Traffic could reach the simulated Internet server.
5. The web service was reachable.

---

# 10. DNS Configuration on Routers and Switches

The DNS server was configured on all routers and switches using:

```cisco
ip name-server 192.168.4.3
```

### Routers configured

* R1
* R2_DHCP
* R3
* ISP_Router
* Router

### Switches configured

* Sales switch
* Marketing switch
* Accounting switch
* Admin switch

### Verification

The configuration was verified with:

```cisco
show running-config | include name-server
```

Expected output:

```text
ip name-server 192.168.4.3
```

---

# 11. Troubleshooting

## PC0 Initially Failed DNS Resolution

During testing, PC0 initially returned:

```text
Host Name Unresolved
```

An `ipconfig /all` check showed:

```text
IPv4 Address........: 169.254.94.174
Default Gateway.....: 0.0.0.0
DHCP Servers........: 0.0.0.0
DNS Servers.........: 0.0.0.0
```

The `169.254.x.x` address indicated that PC0 did not have a normal IPv4 DHCP configuration at that point.

After the network configuration was corrected and PC0 obtained the appropriate network configuration, the browser successfully loaded:

```text
www.ralph.com
```

### Troubleshooting approach

The issue was approached from the bottom up:

1. Check physical/link connectivity.
2. Check interface status.
3. Check IP addressing.
4. Check DHCP.
5. Check default gateway.
6. Check routing.
7. Check DNS server configuration.
8. Test name resolution.
9. Test the application.

This prevented unnecessary configuration changes while troubleshooting.

---

# 12. Useful Cisco IOS Commands

## Interface Status

```cisco
show ip interface brief
```

Used to verify:

* IP addresses
* Interface status
* Protocol status

## Routing

```cisco
show ip route
```

Used to inspect the router's routing table.

## DHCP

```cisco
show ip dhcp pool
show ip dhcp binding
```

Used to verify DHCP pools and active leases.

## Running Configuration

```cisco
show running-config
```

Used to inspect the current device configuration.

## DNS Server Configuration

```cisco
show running-config | include name-server
```

Used to verify the configured DNS server.

## Connectivity

```cisco
ping <IP-ADDRESS>
```

Used to test reachability between network devices.

## CDP

```cisco
show cdp neighbors
```

Used to identify directly connected Cisco devices and interfaces.

---

# 13. Verification Checklist

The completed project was verified through multiple tests.

* [x] R3 interfaces enabled
* [x] R3 interfaces confirmed `up/up`
* [x] Static routing configured
* [x] Inter-router connectivity tested
* [x] DHCP pools created
* [x] DHCP excluded addresses configured
* [x] DHCP relay configured where required
* [x] PC2 received a DHCP address
* [x] Four switch management IPs configured
* [x] Switch default gateways configured
* [x] Sw3 successfully pinged Sw1
* [x] DNS server configured
* [x] `www.ralph.com` record created
* [x] PC0 successfully accessed `www.ralph.com`
* [x] `ip name-server 192.168.4.3` configured on routers
* [x] `ip name-server 192.168.4.3` configured on switches

---

# 14. Project Evidence

Screenshots should be stored in the `evidence/` directory.

Recommended evidence:

```text
evidence/
├── 01-topology.png
├── 02-routing.png
├── 03-dhcp-pc2.png
├── 04-sw3-to-sw1.png
├── 05-dns-records.png
├── 06-pc0-webpage.png
└── 07-sw2-dns-ping.png
```

These screenshots provide visual evidence of the major configuration and verification stages.

---

# 15. Repository Structure

```text
DHCP-DNS-Switch-Configuration/
│
├── README.md
│
├── packet-tracer/
│   └──DHCP-DNS-Switch-Configuration.pkt
│
└── evidence/
    ├── 01-topology.png
    ├── 02-routing.png
    ├── 03-dhcp-pc2.png
    ├── 04-sw3-to-sw1.png
    ├── 05-dns-records.png
    ├── 06-pc0-webpage.png
    └── 07-sw2-dns-ping.png
```

---

# 16. Skills Demonstrated

This project demonstrates practical experience with:

* Cisco IOS configuration
* IPv4 network addressing
* Static routing
* DHCP server configuration
* DHCP address exclusions
* DHCP relay
* `ip helper-address`
* DNS configuration
* DNS A records
* Switch management interfaces
* VLAN 1 SVI configuration
* Default gateways
* Cisco Discovery Protocol
* Network connectivity testing
* ARP-related troubleshooting
* Client IP configuration
* Network service troubleshooting
* End-to-end network validation

---

# 17. What I Learned

This project demonstrated how DHCP, DNS, switching, routing, and IP addressing work together as parts of one network.

One important troubleshooting lesson was that a DNS error does not necessarily mean the DNS server is the problem. When PC0 initially reported that the host name could not be resolved, checking `ipconfig /all` showed that the computer did not have a valid IPv4 configuration, default gateway, DHCP server, or DNS server. This demonstrated the importance of checking the underlying network configuration before changing DNS settings.

The project also reinforced the value of testing after each configuration change. Commands such as `show ip interface brief`, `show ip route`, `show ip dhcp pool`, `show running-config`, `show cdp neighbors`, and `ping` provided a structured way to verify the network.

---

# 18. Portfolio Value

This project demonstrates hands-on experience configuring and troubleshooting a multi-network Cisco environment.

It provides evidence of practical exposure to:

**Network Configuration → Service Configuration → Verification → Troubleshooting**

rather than only theoretical networking knowledge.
