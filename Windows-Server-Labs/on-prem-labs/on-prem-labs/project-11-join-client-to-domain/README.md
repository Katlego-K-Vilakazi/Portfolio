# Project 11 – Join a Client to an Active Directory Domain

## 🎯 Objective
In this project, a Windows 11 virtual machine was successfully joined to the Active Directory Domain created in earlier projects. 
A domain-wide wallpaper was deployed using Group Policy, and a worksheet documenting the required steps was completed.

## 🧰 Tasks Completed

### 1. Verified Network Connectivity
- Ensured the Domain Controller and Windows 11 VM were powered on.
- Confirmed connectivity with `ping` between both systems.

### 2. Joined Windows 11 to the Domain
- Used System → About → Rename this PC (advanced) → Domain.
- Entered domain name and Domain Admin credentials.
- Restarted the machine.
- **Screenshot saved as `domain-membership.png`.**

### 3. Completed Worksheet  
The *Joining a Client to Server 2022* worksheet was completed and saved as:  
- **`VilakaziK M3 Client to Domain.docx`**

### 4. Prepared and Stored Wallpaper Image  
- Chose a domain wallpaper image.
- Saved it in a shared folder on the Domain Controller.
- **Screenshot saved as `wallpaper-image.png`.**

### 5. Created and Applied Group Policy  
- Created a new Group Policy Object linked to the domain.
- Configured the Desktop Wallpaper setting.
- Forced Group Policy update on client:  

- Verified the wallpaper appeared on Windows 11.
- **Screenshot saved as `domain-wallpaper-applied.png`.**

### 6. Project Cleanup
- Disabled the Wallpaper GPO on the Domain Controller.
- Deleted the shared wallpaper folder to reset the environment.

## 📸 Screenshots Included
| File Name | Description |
|----------|-------------|
| `domain-membership.png` | Proof Windows 11 is joined to the domain |
| `wallpaper-image.png` | Wallpaper image that was used for GPO |
| `domain-wallpaper-applied.png` | Wallpaper successfully applied via GPO |
| `VilakaziK M3 Client to Domain.docx` | Completed worksheet |

