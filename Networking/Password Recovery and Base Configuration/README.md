# Cisco Password Recovery & Network Configuration

## Overview

This project was completed in **Cisco Packet Tracer** as part of a networking course practical assignment. It focuses on recovering access to a Cisco router and then configuring and securing the network devices for remote administration and connectivity testing.

The scenario represents a small company with a main office and a classroom building located approximately 250 metres apart. The network includes a main router, multiple switches, laptops, a web server, and an Internet connection.

## Objectives

- Recover access to the Cisco 2900-series main router after a password issue.
- Restore the router configuration from startup configuration.
- Configure hostnames for network devices.
- Configure privileged EXEC passwords and enable secrets.
- Secure console access.
- Configure secure remote access using SSH.
- Enable Cisco Discovery Protocol (CDP).
- Configure switches for remote administration.
- Access and configure the classroom switch remotely from the main office.
- Verify connectivity between network devices.
- Verify Internet connectivity from the classroom laptop.

## Network Addressing

| Device | IP Address |
|---|---|
| Main Router | `192.168.1.1` |
| Network Laptop | `192.168.1.26` |
| Office Switch | `192.168.1.2` |
| Classroom Switch | `192.168.1.3` |
| Classroom Laptop | `192.168.1.25` |
| Web Switch | `192.168.3.2` |
| Web Server | `192.168.3.3` |
| Internet | `192.168.5.2` |

## Technologies & Concepts

- Cisco IOS
- Cisco Packet Tracer
- IPv4 addressing
- Ethernet switching
- Routing
- SSH and Telnet
- Console access
- Cisco Discovery Protocol (CDP)
- Password recovery
- Running and startup configurations
- Interface configuration
- Network connectivity testing
- Remote device administration

## Password Recovery

The main router's password recovery process was performed using the Cisco 2900 password recovery procedure.

The router was interrupted during boot and entered ROMMON mode. The configuration register was temporarily changed to:

```text
0x2142
