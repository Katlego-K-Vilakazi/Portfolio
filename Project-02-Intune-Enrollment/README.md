Project 02 — Intune Device Enrollment (Simulation on Azure VM)
📌 Overview

This project simulates the process of enrolling a Windows device into Microsoft Intune for management.
Because a Microsoft 365 tenant was not available, the full enrollment process could not be performed.
However, all steps, screens, and explanations were completed on an Azure VM to demonstrate full understanding.

This project covers:

Accessing Work or School Enrollment settings

Understanding how Intune enrollment begins

Explaining device registration, compliance, and policy application

Showing the exact UI where enrollment is normally performed

All screenshots are captured from my Azure-hosted Windows VM.

📁 Project Structure
Project-02-Intune-Enrollment/
│
├── README.md
└── screenshots/
      ├── access-work-school.png
      ├── connect-dialog.png
      ├── enrollment-info.png

🖥️ Step-by-Step Simulation Walkthrough
1. Open Enrollment Settings

To start, I opened the Windows Settings as if preparing to enroll the device.

Path:

Settings → Accounts → Access work or school

This is where Windows devices begin Intune enrollment.

📸 Screenshot:
/screenshots/access-work-school.png

2. Initiate Enrollment (Simulation)

I clicked the Connect button.
This is the exact button used in real Intune onboarding.

Normally, the next step prompts for a Microsoft 365 Work/School account, which registers the device with Azure AD and begins Intune enrollment.

Because the tenant is unavailable, this step is demonstrated but not completed.

📸 Screenshot:
/screenshots/connect-dialog.png

Explanation:

This is where I would sign in with my M365 organization account.

After sign-in, the device is automatically joined to Entra ID (Azure AD).

Intune begins installing compliance policies, configuration profiles, and enrolled applications.

3. Understanding the Device Management Panel

Once enrolled, Windows normally displays device management details under:

Access work or school → Info

This area displays:

Device compliance status

Applied configuration profiles

Assigned applications

Synchronization options

📸 Screenshot:
/screenshots/enrollment-info.png

(Screenshot shows where the Intune policies would appear after enrollment.)

📘 Explanation of What Would Happen in Real Intune Enrollment
✔ Device joins Entra ID (Azure AD)

Windows registers the device with the organization.

✔ Device becomes managed by Intune

Management channel opens between Windows and Microsoft Endpoint Manager.

✔ Policies, apps, and compliance check run

This includes:

Password requirements

BitLocker settings

Device health checks

Security baselines

Application deployment

Update configurations

✔ “Access work or school → Info” begins showing policy details

The user can sync or check applied configurations.

📄 Reflection

Although full Intune enrollment was not possible due to the lack of a Microsoft 365 tenant, completing the simulation on an Azure VM provided a clear understanding of:

Where device enrollment begins

How Intune interacts with enrolled Windows devices

What screens and settings are involved

The role of Entra ID during the onboarding process

This demonstrates practical knowledge of Windows device management, even without access to a live M365 environment.
