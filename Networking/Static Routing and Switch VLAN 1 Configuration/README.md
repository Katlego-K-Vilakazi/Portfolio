# Static Routing and Switch VLAN 1 Configuration

This project focuses on configuring a multi-router network using **static routing**, configuring **DHCP services**, assigning management addresses to switches through **VLAN 1**, configuring **DNS**, and verifying end-to-end connectivity.

---

## Project Overview

The objective of this project was to build and verify connectivity across a multi-network Cisco Packet Tracer topology.

The project required:

* Enabling the necessary interfaces on R3
* Planning and configuring routing across all five routers
* Configuring DHCP pools for the required networks
* Configuring DHCP excluded addresses
* Configuring DHCP relay using `ip helper-address`
* Configuring switch management IP addresses
* Configuring switch default gateways
* Testing connectivity between switches
* Creating DNS records for network devices
* Creating the `www.ralph.com` DNS record for the simulated Internet
* Configuring DNS on routers and switches
* Testing DNS name resolution from a switch
* Testing access to the simulated website from PC0

The Packet Tracer topology was configured incrementally, with connectivity tests used to verify the configuration.

---

# Technologies and Concepts

* Cisco Packet Tracer
* Cisco IOS
* IPv4
* Static Routing
* DHCP
* DHCP Relay
* DNS
* Switch Management
* VLAN 1
* Default Gateways
* DNS A Records
* ICMP
* `ping`
* `show` commands
* Network Troubleshooting

---

# Network Topology

The topology contains five routers connected through multiple point-to-point networks.

### Routers

| Device       | Role                                     |
| ------------ | ---------------------------------------- |
| `R1`         | Internal router                          |
| `R2_DHCP`    | Internal router and DHCP server          |
| `R3`         | Internal router / Administration gateway |
| `ISP_Router` | Router toward simulated Internet         |
| `Router 5`   | Simulated Internet-side router           |

### LAN Networks

| Network          |       Gateway | Purpose            |
| ---------------- | ------------: | ------------------ |
| `192.168.1.0/24` | `192.168.1.1` | Sales              |
| `192.168.2.0/24` | `192.168.2.1` | Marketing          |
| `192.168.3.0/24` | `192.168.3.1` | Accounting         |
| `192.168.4.0/24` | `192.168.4.1` | Administration     |
| `192.168.5.0/24` | `192.168.5.1` | Simulated Internet |

---

# IP Addressing

## Router Interfaces

### R1

| Interface |     IP Address | Network           |
| --------- | -------------: | ----------------- |
| Gi0/0     | `192.168.99.1` | `192.168.99.0/30` |
| Gi0/1     |  `192.168.2.1` | `192.168.2.0/24`  |
| Gi0/2     |  `192.168.1.1` | `192.168.1.0/24`  |

### R2_DHCP

| Interface |     IP Address | Network           |
| --------- | -------------: | ----------------- |
| Gi0/0     | `192.168.99.5` | `192.168.99.4/30` |
| Gi0/1     | `192.168.99.2` | `192.168.99.0/30` |
| Gi0/2     |  `192.168.3.1` | `192.168.3.0/24`  |

### R3

| Interface |     IP Address | Network           |
| --------- | -------------: | ----------------- |
| Gi0/0     | `192.168.99.9` | `192.168.99.8/30` |
| Gi0/1     | `192.168.99.6` | `192.168.99.4/30` |
| Gi0/2     |  `192.168.4.1` | `192.168.4.0/24`  |

### ISP_Router

| Interface |      IP Address | Network           |
| --------- | --------------: | ----------------- |
| Gi0/1     | `192.168.99.10` | `192.168.99.8/30` |
| S0/0/0    |    `198.3.24.3` | Internet link     |

### Router 5

| Interface |    IP Address | Network          |
| --------- | ------------: | ---------------- |
| Fa0/0     | `192.168.5.1` | `192.168.5.0/24` |
| S2/0      |  `198.3.24.4` | Internet link    |

---

# 1. Enable R3 Interfaces

The project required the necessary interfaces on R3 to be enabled.

The following interfaces were configured:

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

The required interfaces were verified as operational.

Expected R3 addressing:

```text
Gi0/0    192.168.99.9
Gi0/1    192.168.99.6
Gi0/2    192.168.4.1
```

---

# 2. Static Routing

Static routing was configured to allow the five routers to communicate with all required networks.

The worksheet states that all five routers need routing entries for a fully connected network and allows any working routing configuration.

---

## R1 Static Routes

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.99.2
ip route 192.168.4.0 255.255.255.0 192.168.99.2
ip route 192.168.5.0 255.255.255.0 192.168.99.2
ip route 192.168.99.4 255.255.255.252 192.168.99.2
ip route 192.168.99.8 255.255.255.252 192.168.99.2
```

---

## R2_DHCP Static Routes

The existing route toward the Sales network was retained:

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

---

## R3 Static Routes

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.99.5
ip route 192.168.2.0 255.255.255.0 192.168.99.5
ip route 192.168.3.0 255.255.255.0 192.168.99.5
ip route 192.168.5.0 255.255.255.0 192.168.99.10
ip route 192.168.99.0 255.255.255.252 192.168.99.5
```

---

## ISP_Router Static Routes

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.99.9
ip route 192.168.2.0 255.255.255.0 192.168.99.9
ip route 192.168.3.0 255.255.255.0 192.168.99.9
ip route 192.168.99.0 255.255.255.252 192.168.99.9
ip route 192.168.99.4 255.255.255.252 192.168.99.9
```

---

## Router 5 Static Routes

```cisco
ip route 192.168.1.0 255.255.255.0 198.3.24.3
ip route 192.168.2.0 255.255.255.0 198.3.24.3
ip route 192.168.3.0 255.255.255.0 198.3.24.3
ip route 192.168.4.0 255.255.255.0 198.3.24.3
ip route 192.168.99.0 255.255.255.0 198.3.24.3
```

---

# 3. Verify Routing

Routing tables can be checked with:

```cisco
show ip route
```

A useful connectivity test is:

```cisco
ping <destination-ip>
```

The project required routing to be functional across all five routers.

---

# 4. DHCP Configuration

DHCP was configured on `R2_DHCP`.

The existing Sales DHCP pool was used as the pattern for the additional pools, as specified by the project worksheet.

---

## Sales DHCP Pool

```cisco
ip dhcp excluded-address 192.168.1.1
ip dhcp excluded-address 192.168.1.2

ip dhcp pool Sales
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 192.168.4.3
```

---

## Marketing DHCP Pool

```cisco
ip dhcp excluded-address 192.168.2.1
ip dhcp excluded-address 192.168.2.2

ip dhcp pool Marketing
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 192.168.4.3
```

---

## Accounting DHCP Pool

```cisco
ip dhcp excluded-address 192.168.3.1
ip dhcp excluded-address 192.168.3.2

ip dhcp pool Accounting
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 192.168.4.3
```

---

## Administration DHCP Pool

```cisco
ip dhcp excluded-address 192.168.4.1
ip dhcp excluded-address 192.168.4.2

ip dhcp pool Admin
 network 192.168.4.0 255.255.255.0
 default-router 192.168.4.1
 dns-server 192.168.4.3
```

---

# 5. DHCP Relay

Because the DHCP server is located on R2_DHCP, networks that are not directly connected to the DHCP server require DHCP relay.

The relay is configured with:

```cisco
ip helper-address 192.168.3.1
```

The helper address forwards DHCP broadcasts from the client network toward the DHCP service.

### Marketing

On R1:

```cisco
interface GigabitEthernet0/1
 ip helper-address 192.168.3.1
```

### Administration

On R3:

```cisco
interface GigabitEthernet0/2
 ip helper-address 192.168.3.1
```

The Accounting network is directly connected to R2_DHCP and therefore does not require a relay on that interface.

---

# 6. DHCP Verification

The DHCP pools can be checked with:

```cisco
show ip dhcp pool
```

Active DHCP assignments can be checked with:

```cisco
show ip dhcp binding
```

PC2 was tested as a DHCP client.

The resulting configuration was:

```text
IPv4 Address:      192.168.3.3
Subnet Mask:       255.255.255.0
Default Gateway:   192.168.3.1
DNS Server:        192.168.4.3
```

This confirmed that the Accounting DHCP pool was assigning addresses successfully.

---

# 7. Switch VLAN 1 Configuration

The switches were configured with management IP addresses on their default VLAN, VLAN 1.

This allows the switches to be managed and tested through their Layer 3 management address.

---

## Sw1

```cisco
enable
configure terminal

interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
 exit

ip default-gateway 192.168.1.1

end
```

---

## Sw2

```cisco
enable
configure terminal

interface vlan 1
 ip address 192.168.2.2 255.255.255.0
 no shutdown
 exit

ip default-gateway 192.168.2.1

end
```

---

## Sw3

```cisco
enable
configure terminal

interface vlan 1
 ip address 192.168.3.2 255.255.255.0
 no shutdown
 exit

ip default-gateway 192.168.3.1

end
```

---

## Sw4

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

---

# 8. Verify Switch Management Interfaces

The following command was used to verify switch management configuration:

```cisco
show ip interface brief
```

The expected VLAN 1 addresses were:

| Switch |     VLAN 1 IP | Default Gateway |
| ------ | ------------: | --------------: |
| Sw1    | `192.168.1.2` |   `192.168.1.1` |
| Sw2    | `192.168.2.2` |   `192.168.2.1` |
| Sw3    | `192.168.3.2` |   `192.168.3.1` |
| Sw4    | `192.168.4.2` |   `192.168.4.1` |

---

# 9. Switch-to-Switch Connectivity

The worksheet specifically requires testing connectivity from Sw3 to Sw1.

From Sw3:

```cisco
ping 192.168.1.2
```

Successful replies demonstrate that:

* Sw3 has a valid management IP.
* Sw3 has a valid default gateway.
* The routers know the route toward the Sales network.
* Sw1 is reachable across the routed network.

---

# 10. DNS Configuration

The DNS server is:

```text
192.168.4.3
```

The project requires DNS records for the switches and routers, as well as an alias for the simulated Internet.

The DNS service was configured in Cisco Packet Tracer under:

```text
Server
 └── Services
     └── DNS
```

---

## DNS Records

The following records were configured for the network:

| Hostname                              | Type |      IP Address |
| ------------------------------------- | ---- | --------------: |
| R1                                    | A    |  `192.168.99.1` |
| R2_DHCP                               | A    |  `192.168.99.2` |
| R3                                    | A    |  `192.168.99.9` |
| ISP_Router                            | A    | `192.168.99.10` |
| Router5                               | A    |   `192.168.5.1` |
| Sw1                                   | A    |   `192.168.1.2` |
| Sw2                                   | A    |   `192.168.2.2` |
| Sw3                                   | A    |   `192.168.3.2` |
| Sw4                                   | A    |   `192.168.4.2` |
| [www.ralph.com](http://www.ralph.com) | A    |   `192.168.5.2` |

The worksheet specifically requires the DNS server at `192.168.4.3` to contain records for the switches and routers and an alias for `www.ralph.com` pointing to `192.168.5.2`.

---

# 11. DNS Configuration on Routers and Switches

The DNS server was configured on the routers and switches with:

```cisco
ip name-server 192.168.4.3
```

This allows Cisco IOS devices to use the Packet Tracer DNS server for hostname resolution.

### Verification

```cisco
show running-config | include name-server
```

Expected result:

```text
ip name-server 192.168.4.3
```

---

# 12. Test `www.ralph.com` from PC0

The project requires opening the web browser on PC0 and entering:

```text
www.ralph.com
```

The DNS record resolves the hostname to:

```text
192.168.5.2
```

The successful webpage access confirms that the required network path and DNS configuration are functioning together.

The worksheet specifically requires this browser test as part of the project assessment.

---

# 13. Test DNS from Sw2

The project also requires testing DNS name resolution from Sw2.

From Sw2:

```cisco
ping www.ralph.com
```

A successful response demonstrates that:

1. Sw2 has a valid management address.
2. Sw2 has a valid default gateway.
3. Sw2 has the DNS server configured.
4. The DNS server can resolve `www.ralph.com`.
5. Routing to `192.168.5.2` is working.

---

# 14. Troubleshooting Method

The project provided several opportunities to practice structured troubleshooting.

When a connectivity test failed, the following order was useful:

```text
1. Physical/link status
        ↓
2. Interface status
        ↓
3. IP addressing
        ↓
4. Default gateway
        ↓
5. DHCP
        ↓
6. Routing table
        ↓
7. DNS configuration
        ↓
8. Name resolution
        ↓
9. Application/web service
```

### Useful commands

Check interfaces:

```cisco
show ip interface brief
```

Check routing:

```cisco
show ip route
```

Check DHCP:

```cisco
show ip dhcp pool
show ip dhcp binding
```

Check DNS configuration:

```cisco
show running-config | include name-server
```

Check the complete configuration:

```cisco
show running-config
```

Test connectivity:

```cisco
ping <IP-address>
```

Test hostname resolution:

```cisco
ping www.ralph.com
```

Check directly connected Cisco devices:

```cisco
show cdp neighbors
```

---

# 15. Verification Checklist

The project was completed and tested against the required objectives.

* [x] R3 interfaces enabled
* [x] R3 interface status verified
* [x] Static routing configured
* [x] Routes configured across all five routers
* [x] DHCP pools configured
* [x] DHCP excluded addresses configured
* [x] DHCP relay configured where required
* [x] PC2 successfully received a DHCP address
* [x] Switch management IP addresses configured
* [x] Switch default gateways configured
* [x] Sw3 tested connectivity to Sw1
* [x] DNS server configured
* [x] Router DNS records created
* [x] Switch DNS records created
* [x] `www.ralph.com` configured
* [x] PC0 tested access to `www.ralph.com`
* [x] DNS server configured on routers
* [x] DNS server configured on switches
* [x] Sw2 tested `www.ralph.com` resolution

The project worksheet allocates 20 points across these configuration and verification tasks.

---

# 16. Evidence

Screenshots for the project should be stored in the `evidence/` directory.

Recommended evidence:

```text
evidence/
├── 01-network-topology.png
├── 02-router-connectivity.png
├── 03-pc2-dhcp.png
├── 04-sw3-ping-sw1.png
├── 05-dns-a-records.png
├── 06-pc0-www-ralph.png
└── 07-sw2-ping-www-ralph.png
```

### Evidence descriptions

**01 — Network Topology**

Shows the complete Packet Tracer topology and the links between the network devices.

**02 — Router Connectivity**

Shows successful connectivity between the routers or the routing tables.

**03 — PC2 DHCP**

Shows PC2 receiving its DHCP configuration.

**04 — Sw3 to Sw1**

Shows the successful ping from Sw3 to Sw1.

**05 — DNS A Records**

Shows the DNS records configured on the DNS server.

**06 — PC0 Webpage**

Shows successful access to:

```text
www.ralph.com
```

**07 — Sw2 DNS Ping**

Shows Sw2 successfully resolving and pinging:

```text
www.ralph.com
```

---

# 17. Configuration Files

The `configs/` directory is intended to contain sanitized copies of the configurations used in the project.

Example:

```text
configs/
├── R1.txt
├── R2_DHCP.txt
├── R3.txt
├── ISP_Router.txt
├── Router5.txt
├── Sw1.txt
├── Sw2.txt
├── Sw3.txt
└── Sw4.txt
```

Configuration files should be captured with:

```cisco
show running-config
```

Sensitive information such as passwords should be removed before committing configuration files to a public GitHub repository.

---

# 18. Skills Demonstrated

This project demonstrates practical experience with:

* Cisco IOS
* IPv4 addressing
* Subnetting
* Static routing
* Routing table analysis
* DHCP server configuration
* DHCP address exclusions
* DHCP relay
* `ip helper-address`
* Switch management interfaces
* VLAN 1
* Switch default gateways
* DNS A records
* DNS client configuration
* Hostname resolution
* ICMP testing
* Cisco Packet Tracer
* Network troubleshooting
* End-to-end connectivity testing

---

# 19. Key Learning Outcomes

One of the main lessons from this project was understanding how the different network services depend on each other.

A DHCP client needs access to a DHCP service to receive an IP address, subnet mask, gateway, and DNS server. Devices on different networks then depend on routing to communicate. DNS provides hostname resolution, while the default gateway allows devices such as switches and PCs to reach destinations outside their local subnet.

The project also demonstrated why troubleshooting should be performed systematically. A failed `ping` or DNS lookup does not immediately identify the cause. Checking interfaces, IP addresses, gateways, routing tables, DHCP configuration, and DNS configuration helps narrow down the problem.

---

# 20. Project Workflow

The project followed this general workflow:

```text
Enable R3 Interfaces
        ↓
Configure Static Routing
        ↓
Configure DHCP Pools
        ↓
Configure DHCP Relay
        ↓
Configure Switch VLAN 1
        ↓
Verify Switch Connectivity
        ↓
Configure DNS Records
        ↓
Configure DNS on Routers/Switches
        ↓
Test www.ralph.com
        ↓
Verify DNS from Sw2
        ↓
Document Results
```

---

# 21. Repository Structure

```text
Static-Routing-Switch-VLAN1/
│
├── README.md
│
├── packet-tracer/
│   ├── README.md
│   └──Static-Routing-Switch-VLAN1.pkt
│
│
│
└── evidence/
    ├── README.md
    ├── 01-network-topology.png
    ├── 02-router-connectivity.png
    ├── 03-pc2-dhcp.png
    ├── 04-sw3-ping-sw1.png
    ├── 05-dns-a-records.png
    ├── 06-pc0-www-ralph.png
    └── 07-sw2-ping-www-ralph.png
