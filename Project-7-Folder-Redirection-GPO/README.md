Project 7 — Implement Folder Redirection with Group Policy
Category: Windows Server / Active Directory
Subcategory: Group Policy — Folder Redirection
📌 Overview

This project demonstrates how to implement Folder Redirection using Group Policy in a Windows Server 2022 Active Directory environment.

Folder Redirection ensures that a user’s Documents folder (and optionally Desktop, Pictures, etc.) is stored on a central file server, allowing the user to access their files from any domain-joined workstation.

This solves a common real-world issue where users save files locally and cannot access them from other devices.

🎯 Objectives

✔ Configure a server share for redirection
✔ Create and link a Group Policy Object
✔ Redirect user Documents to a server path
✔ Apply GPO settings and verify redirection
✔ Test access control and exclusive rights
✔ Demonstrate expected “Access Denied” behavior for administrators

🛠 Skills Demonstrated

Active Directory User & Computer management

Group Policy Management

Folder Redirection settings configuration

SMB share creation

UNC path usage

GPO troubleshooting (gpupdate /force, gpresult /v)

NTFS permission interpretation

User experience management in AD

Windows Server 2022 administration

Windows 11 domain login behavior

🧱 Environment Setup

Windows Server 2022 (Domain Controller)

Windows 11 Pro (Domain-joined client)

Active Directory Domain Services installed

Group Policy Management installed

A standard domain user account created

📂 What I Did (Step-by-Step Summary)
1. Created a standard domain user

Using Active Directory Users and Computers (ADUC), I created a test user for verifying redirection.

2. Logged into Windows 11 as the user

Created multiple files in the Documents folder

These files would later be redirected automatically

3. Created a server share for redirection

On Server 2022:

C:\KatlegoRedirection


Shared the folder with Everyone: Read/Write

Recorded the UNC path:

\\SERVERNAME\KatlegoRedirection

4. Created and configured the Folder Redirection GPO

In Group Policy Management:

Created Folder Redirection GPO

Linked to the Domain

Edited User Configuration → Policies → Windows Settings → Folder Redirection → Documents

Target settings:

Basic — Redirect everyone’s folder to the same location

Create a folder for each user under the root path

Root Path: \\SERVERNAME\KatlegoRedirection

Settings:

Grant user exclusive rights

Move contents of/Documents

Policy removal → Redirect back to local profile

5. Forced GPO update

On Server:

gpupdate /force


On Windows 11:

gpresult /v
gpupdate /force


Logged off and back on to enforce the policy.

6. Verified Folder Redirection

On Windows 11:

Opened Documents Properties

Confirmed Location now pointed to the server path:

\\SERVERNAME\KatlegoRedirection\%USERNAME%\Documents


Confirmed all previously created files were present

7. Tested administrator access restrictions

On Server 2022:

Navigated to:

C:\KatlegoRedirection\<username>\Documents


Attempted to open folder → Expected Access Denied error
(because user has exclusive rights)

📷 Screenshots Included

Your project submission will include:

Shared Folder Screenshot (Step 3)

GPO Target Settings (Step 4a)

GPO Settings (Step 4b)

File Properties showing redirected location

Administrator access denied error

(Placeholders if adding to GitHub later)

/screenshots/
   folder_share.png
   gpo_target.png
   gpo_settings.png
   redirected_file_properties.png
   admin_access_denied.png

✅ Results

Folder Redirection successfully configured

Documents now stored on the file server

Users can access documents on any domain-joined workstation

Administrator is correctly blocked from user’s redirected folder

All project requirements completed and verified

🚀 Real-World Relevance

This project demonstrates enterprise-level IT administration skills, including:

Managing GPOs

Roaming user data

File server management

Securing user data

Troubleshooting domain policy application

Folder Redirection is widely used in:

Corporate environments

Schools/universities

Remote work environments

Managed service provider (MSP) environments

📁 Repository Structure
Project-7-Folder-Redirection-GPO/
│
├── README.md
└── screenshots/
      ├── folder_share.png
      ├── gpo_target.png
      ├── gpo_settings.png
      ├── redirected_file_properties.png
      └── admin_access_denied.png
