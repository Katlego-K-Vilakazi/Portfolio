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
```

This allowed the router to boot without loading the existing startup configuration. The saved configuration was then copied into the running configuration so that the existing network configuration could be retained while privileged access was restored.

The normal configuration register value is:

```text
0x2102
```

## Device Configuration

The network devices were configured for remote administration and security. Configuration tasks included:

- Setting device hostnames.
- Creating privileged EXEC passwords and enable secrets.
- Securing console access.
- Creating a local administrative user.
- Configuring a domain name for SSH.
- Generating RSA keys.
- Enabling SSH on VTY lines.
- Restricting unused VTY lines from accepting remote connections.
- Enabling CDP.
- Saving configurations to startup configuration.

The administrative SSH account used during the lab was:

```text
Username: admin
```

## Remote Administration

A key requirement was to configure the classroom switch without physically accessing the classroom building.

The classroom switch was reached remotely from the Network Laptop using:

```text
192.168.1.3
```

Telnet was initially used to obtain remote access and complete the classroom switch configuration. SSH was then configured as the secure remote-management method.

SSH access was verified from the Network Laptop:

```text
C:\>ssh -l admin 192.168.1.3
```

The Main Router was also successfully accessed remotely using SSH:

```text
C:\>ssh -l admin 192.168.1.1

Main-Router#
```

## Verification

The following Cisco IOS commands were used to verify the configuration and network:

```text
show ip interface brief
show cdp neighbors
show cdp neighbors detail
show cdp interface
show interfaces status
show ip ssh
show running-config
show running-config | section line vty
show running-config | include username
ping
```

These commands were used to verify interface status, IP addressing, CDP discovery, SSH configuration, remote-access settings, and connectivity.

## Key Learning Outcomes

### Password Recovery

I gained practical experience recovering access to a Cisco router using ROMMON and the configuration register.

### Secure Remote Administration

I learned how SSH can be configured for authenticated remote administration and why secure management access is important.

### Network Troubleshooting

I used interface status, CDP information, and `ping` tests to understand device connectivity and troubleshoot the network.

### Configuration Management

The project reinforced the difference between the running configuration and startup configuration and the importance of saving a working configuration.

### Practical Networking

The project provided hands-on experience with routers, switches, IP addressing, remote management, and communication between different network segments.

## Project File

The repository contains the Cisco Packet Tracer project:

```text
kVilakazi_Password_Recovery_Project.pkt
```

Open the `.pkt` file with **Cisco Packet Tracer** to inspect the topology and device configurations.

## Skills Demonstrated

- Cisco IOS configuration
- Router and switch administration
- Network troubleshooting
- IPv4 networking
- SSH configuration
- Remote administration
- Password recovery
- CDP verification
- Connectivity testing
- Cisco Packet Tracer
- Network documentation

## Disclaimer

This project was completed in a simulated Cisco Packet Tracer environment for educational purposes. The topology and configurations demonstrate networking concepts and practical troubleshooting skills.
