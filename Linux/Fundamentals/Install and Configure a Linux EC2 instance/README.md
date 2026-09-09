# Linux 2.3 — Install and Configure

A hands-on Linux system administration project completed as part of my BYU-Pathway Worldwide IT coursework.

The project focused on provisioning an Ubuntu Linux virtual server in AWS, connecting to it with SSH, working with the Linux filesystem, managing permissions and ownership, and practicing output redirection, pipes, and process inspection.

> **Note:** This repository intentionally does not contain AWS private keys, passwords, access tokens, public IP addresses, or other credentials.

---

## Project Objectives

This project was divided into four sections:

- **Project 2.3.1 — Create an Ubuntu EC2 Server**
  - Create an Ubuntu EC2 virtual machine in AWS Academy.
  - Connect to the server using SSH.

- **Project 2.3.2 — Basic Linux Commands**
  - Log in to a Linux server through SSH.
  - Create and remove files and directories.
  - List directory contents.
  - Display shell aliases.

- **Project 2.3.3 — Filesystem Navigation, sudo, and Ownership**
  - Navigate the Linux filesystem.
  - Understand and use `sudo`.
  - Create a directory at the root of the filesystem.
  - View and change directory ownership.

- **Project 2.3.4 — Redirects, Pipes, grep, and Processes**
  - Use output redirection.
  - Append command output to a file.
  - Use pipes and `grep`.
  - Display file contents.
  - Review command history.

---

## Environment

| Component | Used |
|---|---|
| Cloud platform | AWS Academy |
| Virtualization | Amazon EC2 |
| Operating system | Ubuntu Linux |
| Remote access | SSH |
| Shell | Bash |
| Primary account | Ubuntu user account |
| Coursework | BYU-Pathway Worldwide |

---

# Project 2.3.1 — Ubuntu EC2 Server

## Objective

Create an Ubuntu Linux EC2 virtual machine in AWS Academy and connect to it using SSH.

## Process

1. Open the AWS Academy session environment.
2. Create a new EC2 instance using Ubuntu.
3. Create/download the SSH key pair.
4. Start the instance.
5. Obtain the instance's connection information.
6. Connect using SSH.

Example SSH syntax:

```bash
ssh -i <private-key.pem> ubuntu@<public-ip-or-dns>