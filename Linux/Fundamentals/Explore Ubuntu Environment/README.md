Explore the Ubuntu Environment

**Video Link:** https://youtu.be/ALIIXu2kIgI

A hands-on Linux system administration project completed as part of my IT coursework.

This project explores the Ubuntu environment through text editors, shell environment variables, package management, user administration, `sudo` privileges, Linux account databases, and command-line documentation.

> **Security note:** This repository does not contain passwords, SSH private keys, AWS credentials, access tokens, or other secrets.

---

## Project Objectives

The project is divided into four sections:

- **VI and Nano**
  - Create an empty file using `touch`.
  - Edit the file with `vi`.
  - Edit and modify the file with `nano`.

- **Environment Variables**
  - Display environment variables.
  - Identify values such as `HOME`, `LANG`, `SHELL`, and `TERM`.
  - Modify root's Bash profile.
  - Observe the difference between `sudo su` and `sudo su -`.

- **Package Management**
  - Update package repositories.
  - Upgrade installed packages.
  - Install additional packages.

- **User Administration**
  - Become the root user.
  - Create a new user.
  - Inspect `/etc/passwd`, `/etc/shadow`, and group information.
  - Grant the new user `sudo` privileges.
  - Configure the user's Bash prompt.
  - Log in as the new administrator account.

---

## Environment

| Component | Used |
|---|---|
| Cloud platform | AWS |
| Virtual machine | Amazon EC2 |
| Operating system | Ubuntu Linux |
| Remote access | SSH |
| Shell | Bash |
| Editors | `vi`, `nano` |
| Package manager | APT |
| Administration | `sudo`, `su` |

---

# VI and Nano

## Objective

Create and edit a `welcome` file using both the `vi` and `nano` editors.

## Step 1 — Log in as the normal user

Connect to the Ubuntu server using SSH.

## Step 2 — Create the welcome file

```bash
touch welcome