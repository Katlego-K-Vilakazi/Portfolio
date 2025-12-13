Final Project: Enterprise Windows Server & Active Directory Deployment
Project Overview

In this project, I designed and deployed a secure, organized enterprise IT environment from the ground up using Windows Server 2025 Datacenter. The goal was to simulate a real-world organizational setup where identity management, access control, and policy enforcement are critical to daily operations.

This project demonstrates my ability to plan, implement, and validate an Active Directory–based environment while following security best practices and the principle of least privilege.

Technologies Used

Microsoft Azure (Virtual Machine)

Windows Server 2025 Datacenter

Active Directory Domain Services (AD DS)

Group Policy Management

NTFS & Share Permissions

Windows Server File Services

Business Scenario

A new Student Success Center required a secure and structured IT environment that:

Centralizes user authentication

Enforces organizational policies

Restricts access to sensitive data

Ensures consistency across staff computers

The environment was built to reflect how a real enterprise domain would be deployed and managed by a systems administrator.

Domain Configuration

Domain Name: ensignsuccess.corp

Active Directory installed and promoted on a new Windows Server VM

Logical OU structure created to separate roles and apply targeted policies

Active Directory Design

Organizational Units (OUs):

Student Success Center

Center Manager

Student Advisors

Admin Accounts

This structure allows for:

Clear role separation

Easier Group Policy targeting

Improved security and scalability

User & Group Management

Accounts Created:

1 × Center Manager

5 × Student Advisors

1 × Backup Domain Administrator

Security Groups:

SSC_Advisors

SSC_Manager

Groups were used to assign permissions instead of individual users, aligning with enterprise best practices.

File Services & Permissions

Shared Folders:

Advising Documents

Read/Write: Student Advisors & Center Manager

Admin Reports

Read/Write: Center Manager only

Permissions were implemented using least privilege, ensuring users only had access required for their role.

Group Policy Implementation

The following policies were enforced using Group Policy Objects (GPOs):

Desktop Standardization

Enforced a standardized desktop background with the organization logo

Browser Configuration

Default homepage set to:
https://www.ensign.edu/information-technology

Password Security

Minimum 12-character passwords

Complexity requirements enabled

These policies ensured a consistent, secure user experience across all staff devices.

Validation & Testing

Verified policy application using gpupdate /force and gpresult

Confirmed redirected access and permissions using test files

Logged in as different users to validate access boundaries

Final Deliverable

A full screen-recorded walkthrough demonstrating:

VM creation

Domain setup

OU and user creation

Folder permissions

Group Policy enforcement

Security validation

📹 Video Demonstration: (Link provided in repository)

Part 1 - https://youtu.be/FqXl9FBVKJY

Part 2 - https://youtu.be/OW9aF3I6MUc

Part 3 - https://youtu.be/KJabioK7-ck

Key Skills Demonstrated

Enterprise Active Directory deployment

Group Policy administration

Secure access control

Identity & role management

Windows Server administration

Troubleshooting and validation
