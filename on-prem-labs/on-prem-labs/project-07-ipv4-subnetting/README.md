# Project 07 — Explain IPv4 Subnetting (Fourth Octet)

## 🎯 Objective
Subnet a 192.168.4.0/24 network into 4 subnets using the fourth octet, demonstrate network connectivity between two PCs, and explain subnetting concepts.

## 🧰 Tasks Completed
1. Configured PC1 with:
   - IP: 192.168.4.25
   - Subnet Mask: 255.255.255.0
   - No Default Gateway or DNS
2. Configured PC2 with:
   - IP: 192.168.4.68
   - Subnet Mask: 255.255.255.0
   - No Default Gateway or DNS
3. Verified initial IP configuration using PowerShell/Command Prompt.
4. Enabled File and Printer Sharing in host firewalls.
5. Successfully pinged PC2 from PC1 and vice versa.
6. Changed subnet mask of both PCs to 255.255.255.192 (/26) to create 4 subnets.
7. Attempted ping after subnetting — failed ping captured (expected due to subnet mismatch).
8. Fixed subnet configuration on one PC to restore connectivity.
9. Verified ping success and captured screenshots of:
   - Initial successful ping
   - Failed ping after subnetting
   - Fixed ping after adjustment

## 📸 Screenshots
- initial-successful-ping.png
- failed-ping-subnetting.png
- fixed-successful-ping.png

## 📝 Notes / Highlights
- Subnetting allows dividing a larger network into smaller, more manageable segments.
- With a /26 mask, each subnet can have 62 usable IP addresses.
- Correct subnet mask alignment is critical for devices to communicate within the same subnet.
- Practical demonstration included troubleshooting connectivity after subnetting.


