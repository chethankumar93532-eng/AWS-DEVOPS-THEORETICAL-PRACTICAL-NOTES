# Day-04 — EC2 Security Groups, NACL & SSH Troubleshooting

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/690acd36-e441-4f84-a628-4999652ca13e" />


## 📌 Day Overview

Day-04 focused on practical **EC2 Security Group configuration and SSH connectivity troubleshooting**.

During this hands-on exercise, I configured inbound SSH access on **TCP port 22**, restricted the source to my public IPv4 address using `/32`, deliberately investigated an SSH timeout, identified an incorrect source IP in the Security Group, corrected the rule, and successfully connected to the Ubuntu EC2 instance.

### What I Practiced

- EC2 Security Groups
- Inbound SSH access
- TCP port 22
- Public IPv4 `/32` source restriction
- SSH connectivity troubleshooting
- `ssh -vvv`
- `nc -vz`
- Public IP verification
- Network path troubleshooting
- Linux `df -h`
- Linux `du -sh`

---

## 🏗️ Architecture

The network path investigated during this hands-on:

```text
Local Mac
    │
    │ SSH / TCP 22
    ▼
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Route Table
    │
    ▼
Network ACL
    │
    ▼
Security Group
TCP 22
Source: Public IPv4 /32
    │
    ▼
EC2 Ubuntu
    │
    ▼
SSH Service
```

👉 **[Open Day-04 Architecture](architecture.md)**

---

## 🛠️ Hands-On

The actual practical work, troubleshooting steps, commands, root cause and final result are documented here:

👉 **[Open Day-04 Hands-On Documentation](AWS-SG-NACL-SSH.md)**

---

## 📚 Study Notes

For detailed interview preparation and senior-level understanding:

👉 **[Open Day-04 Study Notes](Part-01-SG-NACL-Study/AWS-SG-NACL-SSH-Study.md)**

---

## 📸 Screenshots / Hands-On Evidence

The screenshots below are the actual evidence captured during the Day-04 exercise.

| # | Evidence | Open |
|---|---|---|
| 1 | EC2 Instance Verification | [View Screenshot](screenshots/01-ec2-instance-verification.png) |
| 2 | Security Group SSH Rule | [View Screenshot](screenshots/02-security-group-ssh-rule.png) |
| 3 | Security Group Rule Verification | [View Screenshot](screenshots/03-security-group-rule-verification.png) |
| 4 | EC2 Instance Verification | [View Screenshot](screenshots/04-ec2-instance-verification-2.png) |
| 5 | Successful SSH Login | [View Screenshot](screenshots/05-ssh-successful-login.png) |
| 6 | EC2 Instance Connect | [View Screenshot](screenshots/06-ec2-instance-connect.png) |
| 7 | SSH Timeout | [View Screenshot](screenshots/07-ssh-timeout.png) |

---

## 🔗 Day-04 Learning Flow

```text
README
  │
  ├── 🏗️ Architecture
  │       └── Network path
  │
  ├── 🛠️ Hands-On
  │       └── Actual practical work
  │
  ├── 📚 Study Notes
  │       └── Interview preparation
  │
  └── 📸 Screenshots
          └── Practical evidence
```

---

## 🧠 Key Learning

> Before troubleshooting the `.pem` key or username, verify the destination IP, current public IP, Security Group, route table, NACL and TCP/22 connectivity.

The important troubleshooting distinction is:

```text
Network Connectivity
        ↓
TCP 22 Reachability
        ↓
SSH Connection
        ↓
Authentication
```

An SSH timeout can occur **before authentication is even reached**.

---

## ✅ Final Result

```text
Local Mac
   ↓
Public IP Verification
   ↓
Correct Security Group Source IP /32
   ↓
TCP 22
   ↓
EC2 Ubuntu
   ↓
SSH Authentication
   ↓
✅ Successfully Logged In
```

---

## 📂 Repository Structure

```text
Day-04/
├── README.md
├── AWS-SG-NACL-SSH.md
├── architecture.md
├── Part-01-SG-NACL-Study/
│   └── AWS-SG-NACL-SSH-Study.md
└── screenshots/
    ├── 01-ec2-instance-verification.png
    ├── 02-security-group-ssh-rule.png
    ├── 03-security-group-rule-verification.png
    ├── 04-ec2-instance-verification-2.png
    ├── 05-ssh-successful-login.png
    ├── 06-ec2-instance-connect.png
    └── 07-ssh-timeout.png
```
