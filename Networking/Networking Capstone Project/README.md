Networking Capstone Project

## Overview
This project demonstrates the design and implementation of a multi-site enterprise network using Cisco Packet Tracer.

The network includes:
- Multiple routed sites
- RIP v2 dynamic routing
- DHCP centralized server configuration
- VLAN segmentation
- Port security
- Wireless network implementation (WPA2)
- Telnet remote access
- Web server hosting

---

## Technologies Used
- Cisco Packet Tracer
- RIP Version 2
- DHCP
- VLAN & Trunking
- Port Security
- WPA2 Wireless Security
- Telnet
- Subnetting (VLSM)

---

## Network Design
The network was subnetted using VLSM to efficiently allocate address space.

Point-to-point router links used /30 networks to minimize IP waste.

Dynamic routing was implemented using RIP v2 to allow automatic route exchange.

---

## Key Features Implemented

### 1. Dynamic Routing
- RIP version 2
- No auto-summary enabled

### 2. DHCP Configuration
- Centralized DHCP server
- Excluded gateway addresses
- DHCP relay using `ip helper-address`

### 3. Wireless Network
- WRT300N configured as Access Point
- WPA2 Personal authentication
- Laptop successfully obtains DHCP address

### 4. Security
- Port Security limiting MAC addresses
- Encrypted enable secret password
- Telnet remote login configured

---

## Proof of Functionality

See the `screenshots` folder for:
- Successful DHCP assignments
- Routing tables
- Wireless association
- Successful and failed pings
- Telnet session
- Web server access

---

## Lessons Learned
- Importance of proper subnet planning
- DHCP relay configuration
- Wireless troubleshooting in Packet Tracer
- Routing protocol verification using `show ip route`