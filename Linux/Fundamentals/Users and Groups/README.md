# Users and Groups


**Video Link:**  https://youtu.be/0vZUfc7f_9M


## Overview

This project focuses on Linux user and group administration in Ubuntu. User accounts control who can log in to a Linux system and what resources they can access, while groups make it easier to manage permissions for multiple users.

For this project, I used an administrative account with `sudo` privileges to create the required groups and users, assign passwords, and configure group memberships for the PromisedLand office scenario.

The project builds on the user and administrative account configuration completed in **Linux 2.4.4 – Explore the Ubuntu Environment**.

---

## Learning Objectives

By completing this project, I practiced how to:

* Create Linux groups.
* Assign passwords to groups.
* Create regular Linux users.
* Assign passwords to users.
* Add users to supplementary groups.
* Verify users and group memberships.
* Understand how groups simplify system administration.
* Understand the difference between regular users and system users.
* Understand how user and group management connects to file permissions.

---

## Environment

| Item                   | Details                      |
| ---------------------- | ---------------------------- |
| Operating System       | Ubuntu Linux                 |
| Environment            | Ubuntu Virtual Machine       |
| Administrative Account | `admin`                      |
| Privilege Management   | `sudo`                       |
| Project                | Linux 3.3 – Users and Groups |

---

# Project 3.3.1 – Create Groups

## Scenario

PromisedLand is opening an office in Portland, Oregon. The organization needs groups representing different departments and types of users.

The following groups were required:

| Group         | Description            |
| ------------- | ---------------------- |
| `hr`          | Human Resources        |
| `it`          | Information Technology |
| `eng`         | Engineering            |
| `sales`       | Sales                  |
| `callcenter`  | Call Center            |
| `external`    | External Users         |
| `qa`          | Quality Assurance      |
| `finance`     | Finance                |
| `outsourcing` | Outsourcing            |
| `mgmt`        | Management             |

---

## Creating the Groups

The groups were created using the `groupadd` command.

```bash
sudo groupadd hr
sudo groupadd it
sudo groupadd eng
sudo groupadd sales
sudo groupadd callcenter
sudo groupadd external
sudo groupadd qa
sudo groupadd finance
sudo groupadd outsourcing
sudo groupadd mgmt
```

### Why `groupadd`?

`groupadd` creates a new Linux group. Groups can then be used when assigning permissions and organizing users.

---

## Verifying the Groups

The groups can be viewed with:

```bash
getent group
```

A more focused check can be performed with:

```bash
getent group | grep -E '^(hr|it|eng|sales|callcenter|external|qa|finance|outsourcing|mgmt):'
```

This makes it easier to confirm that the required groups exist.

---

# Group Passwords

The assignment requires passwords to be assigned to the following two groups:

* `hr`
* `mgmt`

The `gpasswd` command was used.

```bash
sudo gpasswd hr
sudo gpasswd mgmt
```

The passwords are entered interactively when prompted.

### Why use `gpasswd`?

`gpasswd` is used to administer group-related information, including group passwords and group membership.

---

# Project 3.3.2 – Create Users

## Required Users

The following regular users were required:

|  # | Username  |
| -: | --------- |
|  1 | `ted`     |
|  2 | `sally`   |
|  3 | `bruce`   |
|  4 | `phil`    |
|  5 | `tracey`  |
|  6 | `tammy`   |
|  7 | `holly`   |
|  8 | `cindy`   |
|  9 | `betty`   |
| 10 | `colleen` |
| 11 | `jeanine` |
| 12 | `scot`    |
| 13 | `tim`     |
| 14 | `doug`    |
| 15 | `brandon` |
| 16 | `jared`   |

---

## Creating the Users

Each user was created with a home directory using the `-m` option.

```bash
sudo useradd -m ted
sudo useradd -m sally
sudo useradd -m bruce
sudo useradd -m phil
sudo useradd -m tracey
sudo useradd -m tammy
sudo useradd -m holly
sudo useradd -m cindy
sudo useradd -m betty
sudo useradd -m colleen
sudo useradd -m jeanine
sudo useradd -m scot
sudo useradd -m tim
sudo useradd -m doug
sudo useradd -m brandon
sudo useradd -m jared
```

### Why use `-m`?

The `-m` option tells `useradd` to create a home directory for the new user.

For example:

```text
/home/ted
```

would be the home directory associated with the `ted` account.

---

## Verifying the Users

User information can be checked using:

```bash
getent passwd
```

To specifically check the users created for this project:

```bash
getent passwd | grep -E '^(ted|sally|bruce|phil|tracey|tammy|holly|cindy|betty|colleen|jeanine|scot|tim|doug|brandon|jared):'
```

The `id` command can also be used:

```bash
id ted
```

---

# User Passwords

The assignment specifically requires passwords to be assigned to:

* Ted
* Doug

The password specified by the assignment was:

```text
promisedland123
```

The passwords were assigned using:

```bash
sudo passwd ted
sudo passwd doug
```

The password is entered twice when prompted.

> **Security note:** The password is included here only because it is explicitly part of the course assignment. Real production systems should use strong, unique passwords and should not document actual passwords in a public GitHub repository.

---

# Project 3.3.3 – Assign Users to Groups

## Required Group Memberships

The assignment requires the following memberships:

| User    | Required Group |
| ------- | -------------- |
| Ted     | `hr`           |
| Sally   | `hr`           |
| Bruce   | `eng`          |
| Phil    | `eng`          |
| Phil    | `it`           |
| Holly   | `it`           |
| Cindy   | `it`           |
| Betty   | `mgmt`         |
| Colleen | `mgmt`         |
| Tracey  | `mgmt`         |
| Doug    | `eng`          |
| Doug    | `outsourcing`  |
| Jeanine | `finance`      |
| Tim     | `qa`           |
| Scot    | `external`     |

Some users belong to more than one supplementary group.

In particular:

* **Phil** belongs to `eng` and `it`.
* **Doug** belongs to `eng` and `outsourcing`.

---

## Adding Users to Groups

The `usermod -aG` command was used to add users to supplementary groups.

### Human Resources

```bash
sudo usermod -aG hr ted
sudo usermod -aG hr sally
```

### Engineering

```bash
sudo usermod -aG eng bruce
sudo usermod -aG eng phil
sudo usermod -aG eng doug
```

### Information Technology

```bash
sudo usermod -aG it phil
sudo usermod -aG it holly
sudo usermod -aG it cindy
```

### Management

```bash
sudo usermod -aG mgmt betty
sudo usermod -aG mgmt colleen
sudo usermod -aG mgmt tracey
```

### Outsourcing

```bash
sudo usermod -aG outsourcing doug
```

### Finance

```bash
sudo usermod -aG finance jeanine
```

### Quality Assurance

```bash
sudo usermod -aG qa tim
```

### External

```bash
sudo usermod -aG external scot
```

---

## Understanding `usermod -aG`

The command has two important options:

```bash
usermod -aG
```

* `-a` means **append** the user to the supplementary group.
* `-G` specifies the supplementary group.

Using `-a` is important because it prevents the existing supplementary group memberships from being replaced.

---

# Verification

After configuring the users and groups, the configuration can be checked with the `id` and `groups` commands.

## Using `id`

```bash
id ted
id sally
id bruce
id phil
id doug
id holly
id cindy
id betty
id colleen
id tracey
id jeanine
id tim
id scot
```

The output shows information such as:

* User ID (UID)
* Primary group ID
* Supplementary group memberships

---

## Checking Specific Users

### Phil

```bash
groups phil
```

Phil should show membership in:

```text
eng
it
```

### Doug

```bash
groups doug
```

Doug should show membership in:

```text
eng
outsourcing
```

These checks are useful because both users have more than one required group assignment.

---

# Important Linux Commands Used

| Command    | Purpose                                      |
| ---------- | -------------------------------------------- |
| `groupadd` | Creates a group                              |
| `gpasswd`  | Manages group passwords and group membership |
| `useradd`  | Creates a user                               |
| `passwd`   | Sets or changes a user's password            |
| `usermod`  | Modifies an existing user account            |
| `id`       | Displays user and group identity information |
| `groups`   | Displays a user's group memberships          |
| `getent`   | Retrieves information from system databases  |

---

# Assignment Questions

## 1. What is the difference between a regular user and a system user?

A regular user is normally an account intended for a person to log in and perform normal tasks. A system user is generally used by the operating system or a service to run processes and services rather than being used as a normal interactive user.

---

## 2. How can groups ease the burden of the administrator?

Groups make administration easier because permissions can be assigned to a group instead of configuring each user individually. When users are added to or removed from a group, their access can be managed through the group's permissions.

For example, instead of configuring access separately for every member of the IT department, an administrator can assign the appropriate permissions to the `it` group and then manage membership in that group.

---

## 3. What command can be used to remove a user from a group?

The `gpasswd -d` command can be used to remove a user from a group.

For example:

```bash
sudo gpasswd -d ted hr
```

This removes `ted` from the `hr` group.

---

# Final Project Connection – Users, Groups, and File Permissions

User and group management provides an important foundation for Linux access control.

In a larger Linux deployment, groups can be created around job responsibilities. Users can then be assigned to the appropriate groups based on the access they need.

For example:

```text
HR employees       → hr
IT employees       → it
Engineering        → eng
Management         → mgmt
Finance            → finance
Quality Assurance  → qa
```

This makes it possible to manage access based on a user's role rather than configuring every account individually.

---

## Group Membership and File Permissions

Linux files and directories have permissions for:

```text
Owner
Group
Others
```

For example:

```bash
ls -l
```

may display permissions similar to:

```text
-rw-r----- 1 user finance 1234 example.txt
```

The group associated with the file is `finance`.

If a user belongs to the `finance` group and the group has the appropriate permissions, that user can receive access to the file through their group membership.

This demonstrates why user and group administration is closely connected to file permissions.

---

# Useful Permission Commands

The following commands are relevant when verifying Linux access control:

### Check user identity

```bash
id
```

### Check group membership

```bash
groups
```

### View file permissions

```bash
ls -l
```

### Change file permissions

```bash
chmod
```

### Change file ownership

```bash
chown
```

These commands can be used together to troubleshoot and manage access to files and directories.

---

# Suggested Verification Workflow

A basic verification process for this project is:

```bash
whoami
```

Confirm that the administrative account is being used.

Then verify the groups:

```bash
getent group
```

Verify users:

```bash
getent passwd
```

Check individual memberships:

```bash
id ted
id phil
id doug
id holly
id cindy
```

Check group membership directly:

```bash
groups phil
groups doug
```

This provides evidence that the users and groups were created and configured.

---

# Project Outcome

This project provided practical experience with Linux account and group administration.

The completed configuration demonstrates the ability to:

* Create departmental groups.
* Create regular user accounts.
* Create home directories for users.
* Assign user passwords.
* Assign group passwords.
* Add users to supplementary groups.
* Verify user and group memberships.
* Understand the administrative benefits of groups.
* Connect group membership to Linux file permissions.

---

# Skills Demonstrated

**Linux Administration**

* User management
* Group management
* Account configuration
* Password management
* Sudo administration
* Access control fundamentals

**Linux Commands**

```text
groupadd
gpasswd
useradd
passwd
usermod
id
groups
getent
ls -l
chmod
chown
```

---

# Evidence

Evidence for the project can include screenshots or a screen recording showing:

1. Required groups created.
2. Required users created.
3. `hr` and `mgmt` group passwords configured.
4. Ted and Doug passwords configured.
5. Users assigned to the required groups.
6. `id` or `groups` output verifying memberships.
7. Answers to the three assignment questions.

The course submission requires a short video with the student's own voice-over demonstrating the completed work.

---

## Repository Structure

A simple GitHub structure for this project can be:

```text
Linux-3.3-Users-and-Groups/
│
├── README.md
│
├── commands/
│   └── commands.sh
│
├── evidence/
│   └── README.md
│
└── screenshots/
    └── README.md
```

Screenshots or other evidence should be added only if permitted by the course and should not expose sensitive credentials.

---

## Conclusion

Linux users and groups are fundamental parts of system administration. This project demonstrated how administrators can create accounts, organize users into groups, manage passwords, and verify group memberships.

The main lesson from this project is that groups provide a scalable way to manage access. Instead of configuring permissions individually for every user, administrators can organize users by role and manage access through group membership.
