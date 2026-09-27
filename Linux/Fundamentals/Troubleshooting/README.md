Linux 4.4 – Troubleshooting

Overview

This project demonstrates Linux troubleshooting and system administration tasks performed on an Ubuntu Linux virtual machine.

**Video Link:** https://youtu.be/X9PqmUBc8MI

The project focused on:

Managing user passwords

Managing group passwords and group membership

Reviewing disk partitions and mount points

Collecting disk throughput information

Reviewing basic hardware information

Reviewing running processes

Managing directory ownership and permissions

Using Access Control Lists (ACLs) for user-specific access

The required work was completed using Linux command-line tools and verified through command output.

Project Objectives

The troubleshooting project was divided into three required sections:

4.4.1 – User and Group Management

4.4.2 – Server Profile and System Information

4.4.3 – File Ownership, Permissions, and ACLs

Optional sections covering NFS, Samba, and UFW were not required for the core project.

4.4.1 – User and Group Management

Task 1 – Reset Sally's Password

The first task was to reset Sally's password and verify that the account could be used to log in.

Password Used

PrmLnd2023!

Commands Used

getent passwd sally
sudo passwd sally
su - sally
whoami
exit

Verification

The whoami command returned:

sally

This verified that the password was successfully changed and that the Sally account could be accessed.

Task 2 – Configure the eng Group

The second task was to ensure that the eng group had a password and that Doug could access the group.

Initial Group Check

The existing engineering group was checked with:

getent group eng

The group was initially shown as:

eng:x:1003:bruce

The group shadow information was checked with:

sudo getent gshadow eng

The output showed that the group password had not yet been configured.

Set the Group Password

The engineering group password was set with:

sudo gpasswd eng

The password used for the lab was:

PrmLnd2023!

The group configuration was then checked again with:

sudo getent gshadow eng

The command confirmed that a group password was now configured. The password hash itself is intentionally not included in this README.

Add Doug to the eng Group

Doug's original group membership was checked with:

id doug

Doug was not initially a member of eng.

He was added to the supplementary eng group using:

sudo usermod -aG eng doug

Membership was verified with:

id doug

The output showed eng among Doug's groups.

A login test was also performed:

su - doug
whoami
groups
exit

The whoami command returned:

doug

The groups command showed:

doug eng outsourcing

This verified that Doug was a member of the eng group.

4.4.2 – Server Profile

The second project required a server profile containing:

Disk partitions and mount points

Disk throughput in MB

Basic hardware information

All currently running processes

The commands used to collect the information

Disk Partitions and Mount Points

The block devices and partitions were collected using:

lsblk

The mounted filesystem information was collected using:

df -h

The system showed an 8 GB primary disk named xvda, with the following partitions relevant to the system:

xvda1 – approximately 6.9 GB – mounted at /

xvda13 – approximately 1023 MB – mounted at /boot

xvda14 – approximately 4 MB – no mount point shown

xvda15 – approximately 106 MB – mounted at /boot/efi

Additional disks were also detected:

xvdg – 1 GB

xvdbf – 10 GB

At the time of the project, /data1 and /opt/backup were not shown as mounted filesystems in the df -h output.

Disk Throughput

Disk I/O statistics were collected with:

iostat -m

The -m option reports throughput using MB-based measurements.

The collected output provided read and write activity for the available block devices. For example, the primary xvda device showed approximately 0.72 MB/s read activity and 0.19 MB/s write activity at the time the command was run.

These values represent a snapshot of disk activity and can change as system activity changes.

Basic Hardware Information

Basic hardware information was collected with:

hwinfo --short

This provided a concise summary of the hardware detected by the Ubuntu system.

Running Processes

The running processes were collected with:

ps aux

This command displayed the processes currently running on the system, including information such as the process owner, process ID, CPU usage, memory usage, and command.

Creating /serverprofile

The required information was combined into a single file at the root of the filesystem:

/serverprofile

The file was created with the following command:

sudo sh -c '{
echo "===== SERVER PROFILE ====="
echo
echo "===== 1. DISK PARTITIONS AND MOUNTPOINTS ====="
echo "Command: lsblk"
lsblk
echo
echo "Command: df -h"
df -h
echo
echo "===== 2. DISK THROUGHPUT (MB) ====="
echo "Command: iostat -m"
iostat -m
echo
echo "===== 3. BASIC HARDWARE INFORMATION ====="
echo "Command: hwinfo --short"
hwinfo --short
echo
echo "===== 4. ALL CURRENTLY RUNNING PROCESSES ====="
echo "Command: ps aux"
ps aux
} > /serverprofile'

The resulting file contains clearly separated sections for each required category and identifies the command used to collect each section.

The major sections in the completed profile were:

===== SERVER PROFILE =====
===== 1. DISK PARTITIONS AND MOUNTPOINTS =====
===== 2. DISK THROUGHPUT (MB) =====
===== 3. BASIC HARDWARE INFORMATION =====
===== 4. ALL CURRENTLY RUNNING PROCESSES =====

4.4.3 – Ownership, Permissions, and ACLs

Change /data1 Ownership

The original ownership and permissions of /data1 were checked with:

ls -ld /data1

The directory was originally owned by ubuntu with the group root.

The assignment required Jeanine to become the owner and the finance group to become the group owner.

The ownership was changed with:

sudo chown jeanine:finance /data1

Verification

The change was verified with:

ls -ld /data1

The resulting directory ownership was:

jeanine finance

The directory permissions remained:

drwxr-xr-x

Therefore, the final /data1 directory had Jeanine as the owner and finance as the group owner.

Determine Ted's Permissions on /data1

Ted's account and group membership were checked with:

id ted

Ted was not a member of the finance group.

The directory permissions were checked with:

ls -ld /data1

The directory showed:

drwxr-xr-x

The permission classes can be understood as:

Owner  = rwx
Group  = r-x
Other  = r-x

Because Ted was neither the owner nor a member of the finance group, the other permissions applied to him.

Therefore, Ted has:

r-x

on the /data1 directory.

This means Ted has read and execute permissions on the directory. He can list the directory contents and traverse/enter the directory, but he does not have directory write permission to create, delete, or rename directory entries.

The permissions of individual files inside /data1 are separate from the directory permissions and must be checked independently.

Give Sally rwx Access Using ACL

An Access Control List was used to give Sally read, write, and execute access to /data1 without changing the existing owner and group.

The ACL was configured with:

sudo setfacl -m u:sally:rwx /data1

The ACL was verified with:

getfacl /data1

The relevant ACL entries were:

user::rwx
user:sally:rwx
group::r-x
mask::rwx
other::r-x

The important entry is:

user:sally:rwx

This confirms that Sally has read, write, and execute permissions on /data1 through the ACL.

Troubleshooting Questions

1. What filesystem rights does Ted have for /data1?

Ted has r-x permissions on the /data1 directory because he is neither the directory owner nor a member of the finance group.

He can list and traverse the directory, but he does not have directory write permission.

The permissions of individual files inside the directory may be different and should be checked separately.

2. Give two possible reasons Bruce no longer has access to the eng group.

Two possible reasons are:

An administrator removed Bruce from the eng group's membership.

The group or account configuration was changed or recreated, causing Bruce's previous group membership to be lost.

The exact reason would need to be confirmed by reviewing the current group configuration and any relevant administrative changes.

3. Give two recommendations for future datacenter upgrades.

Recommendation 1 – Verify Linux Compatibility

Before purchasing or deploying new hardware or technologies, verify that the equipment and software are compatible with the Linux distribution and version being used.

This can help prevent compatibility problems after an upgrade.

Recommendation 2 – Test Before Production Deployment

Perform upgrades and compatibility testing in a non-production environment before introducing the changes into the production datacenter.

Testing first provides an opportunity to identify compatibility and configuration problems without immediately affecting production systems.

Key Linux Commands Learned

Command

Purpose

getent passwd

Retrieves user account information

passwd

Changes a user's password

su

Switches to another user account

getent group

Retrieves group information

gpasswd

Manages group passwords and group membership

gshadow

Provides protected group account information

usermod -aG

Adds a user to a supplementary group

id

Displays user and group membership information

lsblk

Displays block devices, partitions, and mount points

df -h

Displays filesystem disk usage

iostat -m

Displays disk I/O statistics and throughput in MB

hwinfo --short

Displays summarized hardware information

ps aux

Displays currently running processes

chown

Changes file or directory ownership

ls -ld

Displays directory permissions and ownership

setfacl

Creates or modifies ACL permissions

getfacl

Displays ACL permissions

Troubleshooting Approach

A major lesson from this project was the importance of checking the actual system configuration instead of assuming that a configuration is correct.

A practical troubleshooting process used in this project was:

Check the current configuration.

Identify what needs to change.

Make the required configuration change.

Verify the change with a suitable command.

Test the affected account or resource when appropriate.

Review permissions, ownership, group membership, and ACLs separately.

Document the system information and commands used.

For example, changing the ownership of /data1 was followed by ls -ld /data1 to verify the owner and group. Similarly, adding Doug to eng was followed by id doug and a login test to confirm the group membership.

This evidence-based approach makes it easier to determine whether a configuration change worked and helps identify where a problem exists when it does not.

Project Verification Checklist

The following required project items were completed:

Sally's password reset and login verified

eng group password configured

Doug added to eng

Doug's eng membership verified

Disk partitions and mount points collected

Disk throughput collected with iostat -m

Basic hardware information collected with hwinfo --short

Running processes collected with ps aux

/serverprofile created

Jeanine made owner of /data1

finance made group owner of /data1

Ted's /data1 directory permissions identified

Sally given rwx ACL access to /data1

ACL verified with getfacl

Project Summary

This project provided practical experience with Linux user and group management, server information gathering, filesystem permissions, ownership, and Access Control Lists.

The required troubleshooting tasks were completed and verified through Linux command-line output. The project also demonstrated the importance of documenting system information, checking actual permissions and group membership, and verifying configuration changes instead of relying on assumptions.

Environment

Operating System: Ubuntu Linux

Environment: Virtual Machine

Primary Interface: Linux command line

Project Area: Linux System Administration and Troubleshooting