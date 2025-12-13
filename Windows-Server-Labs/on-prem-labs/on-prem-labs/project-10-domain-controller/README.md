# Project 10 — Explore a Windows Server Domain Controller

## 🎯 Objective
Familiarize with the Domain Controller (DC) created in Project 9, explore Active Directory, create users and Organizational Units (OUs), and test Remote Desktop access.

## 🧰 Tasks Completed
1. Logged into the Domain Controller using the Administrator account.
2. Opened Server Manager and noted Remote Desktop status and IP address.
3. Opened "Active Directory Users and Computers":
   - Created a unique Domain Admin user account with password `MSPress#1`.  
   - Screenshot saved as `new-user-object.png`.
4. Created a new Organizational Unit (OU) in Active Directory.  
   - Screenshot saved as `new-ou-object.png`.
5. Deleted the newly created OU.  
   - Screenshot saved as `deleted-ou.png`.
6. Enabled Remote Desktop on the Domain Controller and logged in from Windows 11 VM using the newly created admin account.  
   - Screenshot saved as `rdp-session.png`.

## 📸 **Screenshots**
1. `new-user-object.png` → Screenshot of the new Domain Admin user.  
2. `new-ou-object.png` → Screenshot of the newly created OU.  
3. `deleted-ou.png` → Screenshot showing OU deleted.  
4. `rdp-session.png` → Screenshot of successful Remote Desktop session from Windows 11 PC to DC.

## 📝 Notes / Highlights
- Ensured Network Level Authentication was enabled for RDP.
- Verified IP address using Server Manager and PowerShell `ipconfig`.
- Carefully explored AD Users and Computers without changing critical settings.

