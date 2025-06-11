# Evilginx2 + Mythic C2 Offensive Security Lab

## Overview
This repository documents the setup of an offensive security lab environment using Evilginx2 (phishing proxy) and Mythic C2 (Command & Control framework) hosted on AWS. The goal is to demonstrate realistic infrastructure deployment, payload development, and the challenges of live phishing simulation — all for educational and research purposes.

> **Note:** This is a red team research lab. Do not use this infrastructure against any real-world targets without explicit authorization.

---

## Lab Architecture

```
[Attacker] <--> [AWS Ubuntu EC2: Evilginx2] <--> [DNS: Route53]
                                    |
                                    +--> [Mythic C2 Server w/ Athena Payload]
```

### Components
- **Evilginx2** – Phishing proxy with custom phishlets (Outlook)
- **Route 53** – AWS DNS zone management
- **Mythic C2** – Command and control server
- **Athena** – Cross-platform C# payload for Mythic
- **Windows VM** – Payload execution test environment (VirtualBox)

---

## Goals
- ✅ Stand up a phishing server on AWS
- ✅ Register a domain and configure subdomains via Route 53
- ✅ Launch Evilginx2 with a working phishlet (Outlook)
- ✅ Deploy Mythic C2 and build Athena payload
- ✅ Simulate payload execution from a Windows VM

---

## Infrastructure Setup

### 1. AWS EC2 Setup
- OS: Ubuntu 22.04
- Security Group Ports Open: `80`, `443`, `22`
- Subnet, VPC, and Route Table properly configured

### 2. Domain + DNS
- Domain: `offsecuritylab.link`
- Subdomains:
  - `outlook.offsecuritylab.link` → Evilginx2
  - `login.offsecuritylab.link` → Optional
  - `account.offsecuritylab.link` → Optional
- A Records pointed to EC2 public IP (e.g., `3.148.228.16`)

### 3. Evilginx2 Setup
```bash
sudo evilginx -p ./phishlets/custom/
> phishlets hostname outlook outlook.offsecuritylab.link
> phishlets enable outlook
```
- Evilginx2 successfully launched with a custom Outlook phishlet
- TLS setup performed using Let's Encrypt during a successful session

### 4. Mythic C2 Setup
```bash
git clone https://github.com/its-a-feature/Mythic.git
cd Mythic
sudo ./mythic-cli install
sudo ./mythic-cli start
```
- Configured to allow external connections (bind_localhost_only = false)

### 5. Athena Payload
- Built from Mythic UI
- Minimal commands included for stealth:
  - `whoami`, `ifconfig`, `exec`, `upload`, `download`, etc.
- Payload transferred to Windows VM for execution test

---

## Screenshots
- ![image](https://github.com/user-attachments/assets/3601eb01-0d79-4212-84e9-9f6edfd23db0)
- ![image](https://github.com/user-attachments/assets/51bb28d8-0845-46da-8715-cb32d2e51edf)
- ![image](https://github.com/user-attachments/assets/b7222376-9d72-4e79-bb38-2aa02bab88d6)


---

## Limitations & Troubleshooting
- **Phishlet Activation:** Works locally; confirmed resolution and routing via AWS DNS
- **Inbound traffic:** EC2 security group confirmed for ports 80/443; DNS propagation required patience

### Mitigations
- Use verified A records to route phishing domains
- Self-monitor TLS rate limits during cert generation
- VM-based payload test ensures no risk to local machine

---

## Next Steps
- Implement full redirector with nginx proxy
- Test alternative phishlets
- Explore advanced payload types and callback methods
- Expand with cobalt-strike-style operations
