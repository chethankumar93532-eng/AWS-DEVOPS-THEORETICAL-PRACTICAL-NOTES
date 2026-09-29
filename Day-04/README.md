# Day-04 — AWS Security & SSH Troubleshooting

Day-04 focuses on **EC2 Security Groups, Network ACLs and practical SSH connectivity troubleshooting**.

The hands-on exercise used a real SSH timeout, verbose SSH troubleshooting, TCP/22 testing, public-IP verification and correction of an incorrect Security Group source IP.

---

## Day-04 Learning Flow

```text
Security Group
      ↓
SSH / TCP 22
      ↓
Public IP Verification
      ↓
Network Path
      ↓
NACL / Route
      ↓
TCP Connectivity Test
      ↓
SSH Verbose Troubleshooting
      ↓
Fix Incorrect Source IP
      ↓
Successful SSH Login
```

---

## Architecture / Network Path

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
   │
   ▼
EC2 Ubuntu
   │
   ▼
SSH Service
   │
   ▼
SSH Authentication
```

### Security Group Rule Used

```text
Inbound
Protocol: TCP
Port: 22
Source: Correct Public IPv4 /32
```

---

## Part-01 Study Notes

The detailed interview-preparation study is kept separately, following the same Day-03 structure:

```text
Part-01-SG-NACL-Study/
└── AWS-SG-NACL-SSH-Study.md
```

The study covers:

- WHY — Problem Solved
- HOW — Architecture
- LEARN & IMPLEMENT
- HANDS-ON PROOF GATE — Break/Fix
- WHAT-IF — Failure & Troubleshooting
- SENIOR DECISION LAYER
- INTERVIEW ANSWER
- COMMANDS USED / PRACTICE COMMANDS
- Memory Hook

---

## Hands-On Evidence

The original hands-on record is kept separately:

```text
AWS-SG-NACL-SSH.md
```

It documents the actual Day-04 activity:

- Security Group TCP/22 configuration
- Public IPv4 `/32`
- SSH timeout
- `ssh -vvv`
- `nc -vz`
- `curl -4 ifconfig.me`
- Incorrect source IP discovery
- Security Group correction
- Successful SSH connectivity
- `df -h`
- `du -sh`

---

## Screenshots

Screenshots remain separate from the study notes, exactly like Day-03:

```text
screenshots/
```

The screenshot files should use sequential names such as:

```text
01-security-group-ssh-rule.png
02-ec2-instance-verification.png
03-public-ip-verification.png
04-ssh-timeout.png
05-ssh-vvv-troubleshooting.png
06-nc-port-22-test.png
07-security-group-source-ip-correction.png
08-ssh-successful-login.png
09-disk-usage-df.png
10-directory-usage-du.png
```

> The supplied Day-04 DOCX itself does not contain embedded screenshot files, and the Day-04 ZIP contains only the DOCX. I have therefore **not invented or generated replacement screenshots**. Add the actual Day-04 screenshots you captured to this folder using the names above.

---

## Interview Focus

Be able to explain:

```text
SSH Timeout
   ↓
Destination IP
   ↓
Client Public IP
   ↓
Security Group
   ↓
Route Table
   ↓
NACL
   ↓
TCP/22
   ↓
SSH Service
   ↓
Authentication
```

Most important distinction:

> **Network connectivity failure and SSH authentication failure are different problems.**

---

## Commands Used

```bash
ssh -vvv <user>@<EC2-Public-IP>

nc -vz <EC2-Public-IP> 22

curl -4 ifconfig.me

df -h

du -sh
```

---

## 🧠 Memory Hook

```text
SSH Failure
     ↓
IP
 ↓
SG
 ↓
Route
 ↓
NACL
 ↓
TCP/22
 ↓
SSH
 ↓
Authentication
```

**One sentence to remember:**

> **“For an EC2 SSH timeout, prove network reachability first and troubleshoot authentication only after TCP/22 is reachable.”**
