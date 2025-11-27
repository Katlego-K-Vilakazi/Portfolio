# Project 06 — Create a Virtual Peer-to-Peer Network

## 🎯 Objective
Connect at least three Windows 11 VMs into a peer-to-peer (P2P) network (Workgroup) to share files and test user-specific permissions.

## 🧰 Tasks Completed
- Configured all VMs with a common administrator user: Admin / MSPress#1
- Added all VMs to the default Workgroup
- Enabled network discovery and configured file sharing
- Created a "Shared" folder on each VM and saved a meme
- Set file sharing permissions to allow access only for specific users:
  - Computer 1 → User3
  - Computer 2 → User1
  - Computer 3 → User2
- Accessed shared folders from other VMs and confirmed proper permissions
- Documented failed access attempts when incorrect users tried to access the shared folder
- Captured screenshots of:
  1. Workgroup network showing other VMs
  2. Shared folder configuration
  3. Accessed meme file from another VM

## 📸 Screenshots
- workgroup-network.png
- shared-folder-config.png
- accessed-meme.png

## 📝 Executive Summary (Highlights)
- Overview: Created a P2P network to share files among multiple VMs without a domain controller.
- Problem Summary: Needed to ensure only specific users could access files while others could not.
- Analysis Findings: Challenges included network discovery issues and permissions troubleshooting.
- Conclusion: Successfully configured the Workgroup and verified secure file-sharing between VMs.


