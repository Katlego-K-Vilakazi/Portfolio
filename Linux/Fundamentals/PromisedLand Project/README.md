# PromisedLand.com Linux File Server

> **Project type:** Hands-on lab
> **Platform:** Ubuntu Server on AWS EC2
> **Purpose:** Demonstrate foundational Linux administration, storage
> management, access control, Samba file sharing, and host security.

**Video Link:** https://youtu.be/0OBaJOreYDg

## Overview

This project involved planning and configuring a Linux file server for
PromisedLand.com, an organization described in the coursework as having
38 employees. The server was hosted on AWS EC2 and used a separate
Amazon EBS volume for shared departmental data.

Four department shares were configured: `Infrastructure`, `Operations`,
`Applications`, and `CRM`. Linux users and groups, directory
permissions, ACLs, Samba, UFW, package management, a custom login
message, and a scheduled maintenance check were used to demonstrate core
system administration tasks.

This repository documents the configuration and testing completed in the
lab. It is an educational project, not a claim that the environment is
production-ready.

## Objectives

-   Deploy and access an Ubuntu server on AWS EC2 using SSH.
-   Mount a separate volume for shared files and configure persistent
    mounting.
-   Create departmental groups, users, and shared directories.
-   Configure group-based directory permissions and ACLs.
-   Install and configure authenticated Samba shares.
-   Apply host firewall rules and review AWS security-group exposure.
-   Update packages and validate system health.
-   Configure a custom Message of the Day (MOTD) and a scheduled
    maintenance task.
-   Test the result and document limitations.

## Architecture

``` text
Administrator
    |
    | SSH (TCP 22; source restricted in AWS security group)
    v
+-----------------------------------------+
| AWS EC2 - Ubuntu Server                 |
|                                         |
| Samba / Linux users and groups          |
| POSIX permissions / ACLs / UFW          |
|                                         |
| /                Root filesystem        |
| /srv/shares      Separate EBS volume    |
|   |                                     |
|   +-- Infrastructure                    |
|   +-- Operations                        |
|   +-- Applications                      |
|   +-- CRM                               |
+-----------------------------------------+
```

### Environment

-   **Operating system:** Ubuntu Server 26.04.1 LTS (`resolute`), as
    observed during the lab.
-   **Compute:** AWS EC2, 2 vCPUs and approximately 908 MiB RAM.
-   **Root volume:** Approximately 8 GiB.
-   **Shared-data volume:** Separate 10 GiB EBS volume, formatted as
    ext4.
-   **Mount point:** `/srv/shares`.
-   **File-sharing service:** Samba (`smbd`).
-   **Host firewall:** UFW.

IP addresses, SSH private keys, passwords, and other credentials are
intentionally excluded.

## Technologies and Tools

  -----------------------------------------------------------------------
  Technology                          Use
  ----------------------------------- -----------------------------------
  Ubuntu Server                       Linux operating system

  AWS EC2                             Hosts the server

  Amazon EBS                          Separate persistent storage for
                                      shared data

  SSH                                 Remote administration

  Samba                               SMB file sharing

  Linux users and groups              Identity and departmental access
                                      management

  POSIX permissions and SGID          Group-based directory access and
                                      group inheritance

  ACL utilities (`setfacl`,           Additional access-control entries
  `getfacl`)                          

  UFW                                 Host firewall

  `apt`                               Package installation and updates

  `systemd`                           Service and socket status checks

  `cron`                              Scheduled maintenance check

  `smbclient`                         Testing Samba shares

  `mutt`                              Command-line email-client package
                                      installation exercise
  -----------------------------------------------------------------------

## Storage Design

A separate 10 GiB EBS volume was formatted as ext4 and mounted at
`/srv/shares`. The filesystem was identified by UUID and added to
`/etc/fstab` so it would mount persistently after reboot.

The lab's `/etc/fstab` entry was:

``` fstab
UUID=d15212d9-f286-4197-800d-28800d298a73 /srv/shares ext4 defaults,nofail 0 2
```

**Important:** This UUID belongs to the lab volume. Use the UUID
returned by `blkid` in a new deployment; do not copy this value blindly.

Directory layout:

``` text
/srv/shares/
├── Infrastructure/
├── Operations/
├── Applications/
├── CRM/
└── lost+found/
```

Department directories were owned by `root` and the matching department
group, with mode `2770`. The SGID bit helps new items inherit the
directory's group. The `770` permissions grant access to the owner and
group while denying access to other users, subject to ACLs and the
effective permission mask.

Useful checks:

``` bash
lsblk -f
findmnt /srv/shares
df -h / /srv/shares
grep /srv/shares /etc/fstab
```

## Users, Groups, and Permissions

The department groups were:

  Group              Purpose
  ------------------ ----------------------
  `Infrastructure`   Infrastructure share
  `Operations`       Operations share
  `Applications`     Applications share
  `CRM`              CRM share

Employee accounts were based on the names included in the assignment
materials. `brian.hoang` was included in both `Operations` and
`Infrastructure`, reflecting the dual assignment shown in the source
organization chart.

Example directory setup:

``` bash
sudo chown root:Operations /srv/shares/Operations
sudo chmod 2770 /srv/shares/Operations
```

Equivalent ownership and permissions were applied to the other
department directories.

### ACL for Carrie

The account `carrie` was not added to all department groups. ACL entries
were used to grant directory traversal/read access across department
directories and set read-oriented default ACL entries for new content:

``` bash
for department in Infrastructure Operations Applications CRM; do
    sudo setfacl -m u:carrie:rx "/srv/shares/$department"
    sudo setfacl -d -m u:carrie:r-X "/srv/shares/$department"
done
```

Inspect ACLs with:

``` bash
getfacl /srv/shares/Operations
```

Default ACLs affect newly created items; they do not automatically
correct permissions on every existing file. A directory ACL also does
not guarantee that every file beneath it is readable by Carrie, so
file-level access must be tested.

## Samba Configuration

Samba was installed and configured as a standalone file server. Each
department had its own share, with authenticated access restricted to
the relevant group and `carrie`.

Simplified example:

``` ini
[Operations]
    path = /srv/shares/Operations
    browseable = yes
    read only = no
    guest ok = no
    valid users = @Operations carrie
    force group = Operations
    create mask = 0660
    directory mask = 2770
    inherit acls = yes
```

Equivalent sections were configured for `Infrastructure`,
`Applications`, and `CRM`.

Key choices: - Guest access disabled. - Each share restricts permitted
users. - Department groups control access to their corresponding
share. - Files are created with group-writable permissions. -
Directories use the SGID bit and group-writable permissions. - ACL
inheritance is enabled.

Validation and testing commands:

``` bash
sudo testparm -s
sudo systemctl is-active smbd
smbclient -L localhost -U bill.fluharty
smbclient //localhost/Operations -U bill.fluharty
```

Samba credentials are separate from Linux passwords. Creating a Linux
account does not automatically provision it for Samba. An administrator
can add an intended account interactively with:

``` bash
sudo smbpasswd -a username
```

Do not put passwords in scripts, source control, or this README.

## Security

### UFW and AWS security groups

UFW was enabled with incoming traffic denied by default and outgoing
traffic allowed by default. OpenSSH was allowed for administration.

Check the current host firewall configuration with:

``` bash
sudo ufw status verbose
```

The AWS security group is separate from UFW. The inbound SMB rule on TCP
port 445 was removed, and the SSH rule was restricted to the
administrator's current public IP.

**Operational consequence:** With inbound TCP 445 removed from the
security group, remote SMB clients cannot reach the shares through that
AWS network rule. Shares were tested locally on the server. Do not
expose SMB directly to the public internet; a real deployment should use
an appropriately secured private network or VPN with tightly scoped
network rules.

Other measures included disabling Samba guest access, requiring SMB2 or
later, using department permissions and ACLs, checking that ordinary
employee accounts lacked sudo privileges, updating packages, and
displaying an authorized-use notice at login.

A production environment would require additional review of identity
management, logging, monitoring, backups and restore testing, patch
policy, encryption, network segmentation, and periodic access reviews.

## Maintenance

### Package management

Package indexes were refreshed and upgrades were performed during the
lab. The following commands were used to check package health:

``` bash
sudo apt-get check
sudo dpkg --audit
```

These commands check package consistency; they do not update packages
themselves. Updates were performed separately with `apt update` and
`apt upgrade`.

### Custom MOTD

The script `/etc/update-motd.d/99-promisedland` displayed a
PromisedLand.com banner and an authorized-use notice:

``` sh
#!/bin/sh
printf '\\n'
printf '%s\\n' '============================================'
printf '%s\\n' '       PromisedLand.com Linux Server'
printf '%s\\n' '============================================'
printf '%s\\n' 'Authorised access only.'
printf '%s\\n' 'Use this system in accordance with company policy.'
printf '\\n'
```

The script was made executable. During SSH login testing, the banner
appeared twice because of the existing MOTD/PAM setup. The update-motd
script itself ran once; the duplicate login display is a known
presentation issue.

### Biennial reboot check

A root cron entry was configured to run a date-checking script at 03:00
on January 1 each year:

``` cron
0 3 1 1 * /usr/local/sbin/two-year-reboot-check
```

The script checks whether the year is even and only initiates a reboot
when its date and hour conditions match. A dry-run option was included
to test the condition without rebooting:

``` bash
DRY_RUN=1 /usr/local/sbin/two-year-reboot-check
```

Testing outside the scheduled date confirmed that the date guard
prevented a reboot. Any real scheduled reboot should be reviewed against
service availability requirements and supported by monitoring and a
recovery plan.

## Validation and Results

  -------------------------------------------------------------------------------------------
  Area                    Test                                        Result observed
  ----------------------- ------------------------------------------- -----------------------
  Samba configuration     `sudo testparm -s`                          Configuration loaded
                                                                      successfully

  Samba service           `sudo systemctl is-active smbd`             Active

  Share listing           `smbclient -L localhost -U bill.fluharty`   Four department shares
                                                                      listed

  Operations access       Tested using different Samba accounts       Bill could list and
                                                                      write in Operations

  Carrie access           Listed directory and attempted file         Listing worked; write
                          creation                                    attempt was denied

  Storage                 `findmnt`, `df -h`, `/etc/fstab`            Separate ext4 volume
                                                                      mounted at
                                                                      `/srv/shares`

  Persistence             Checked mount after reboot                  Mount remained
                                                                      available

  Firewall                `sudo ufw status verbose`                   Active; incoming denied
                                                                      by default; SSH allowed

  Sudo restrictions       `sudo -l -U carrie` and                     Neither account had
                          `sudo -l -U bill.fluharty`                  sudo privileges

  Package health          `apt-get check`, `dpkg --audit`             No dependency errors or
                                                                      audit findings reported

  MOTD                    Ran update-motd script                      Banner displayed;
                                                                      duplicate login display
                                                                      observed

  Reboot check            Ran outside scheduled date                  Date guard prevented
                                                                      reboot
  -------------------------------------------------------------------------------------------

These results reflect the checks performed in this lab. They do not
establish that every possible account, file, network path, or production
failure scenario was tested.

## Important Implementation Notes

-   The assignment described 38 employees, but the supplied organization
    chart did not provide 38 distinct employee names. The account list
    therefore reflects the names available in the project materials and
    is not a complete 38-person roster.
-   At final testing, Samba credentials had been created for `carrie`
    and `bill.fluharty`. Other Linux employee accounts still required
    individual Samba account provisioning before they could authenticate
    to Samba.
-   Inbound TCP 445 was not permitted by the final AWS security-group
    configuration. Local tests succeeded, but remote SMB access across
    the network was not demonstrated.
-   Carrie's directory ACLs and default ACLs do not by themselves prove
    read access to every existing file. File-level access needs
    additional testing.

These limitations are documented to distinguish what was configured and
tested from what remains necessary for a complete deployment.

## Reproducing the Lab

This is a high-level outline for recreating a similar environment. Adapt
it to your own AWS account and network.

1.  Launch a supported Ubuntu Server EC2 instance and restrict SSH
    access to a trusted source IP or management network.
2.  Attach a separate EBS volume, identify it with `lsblk` and `blkid`,
    format it if new, and mount it at `/srv/shares`.
3.  Add the new filesystem's UUID to `/etc/fstab` and verify it with
    `findmnt`.
4.  Install Samba and ACL utilities.
5.  Create department groups and users from an authoritative roster.
6.  Create department directories, set ownership and SGID permissions,
    and apply required ACLs.
7.  Configure Samba shares with guest access disabled and appropriate
    group restrictions.
8.  Provision Samba credentials for every intended SMB user.
9.  Configure UFW and AWS security groups together; keep SMB off the
    public internet.
10. Validate the configuration with `testparm` and test access to each
    share with representative accounts.
11. Configure and verify the MOTD and scheduled maintenance tasks.
12. Document test results, account status, limitations, backups, and
    recovery steps.

## What I Learned

This project helped me connect Linux administration concepts in a
working environment. I practised using SSH to access an AWS-hosted
Ubuntu server, mounting separate storage, managing users and groups,
setting permissions and ACLs, configuring Samba, applying firewall
rules, managing packages, and validating service status.

One of the main lessons was to test the result of each configuration
step. A command completing successfully does not necessarily prove that
a service or permission behaves as intended. Testing with different
accounts helped reveal the difference between configuring access and
verifying it.

## Possible Improvements

If the project were extended, the next steps would be to:

-   Reconcile accounts against an approved roster of all 38 employees.
-   Provision and test Samba credentials for each authorized user.
-   Test Carrie's access to existing and newly created files across
    every share.
-   Perform remote client tests from a properly secured private network.
-   Resolve the duplicate MOTD display.
-   Implement backups and test restoration.
-   Add routine log review, monitoring, and documented patch/reboot
    procedures.
-   Review the design against production requirements before using real
    organizational data.

## Disclaimer

This repository describes a lab completed for portfolio demonstration. It contains no access credentials and should not be treated as a production deployment guide. Validate all commands and configuration against your own environment before using them.