# Day-04 — Security Group & NACL SSH Troubleshooting Hands-On

## Activities Completed

- Worked with AWS EC2 Security Groups.
- Configured inbound SSH access.
- Used TCP port `22`.
- Restricted the source to the public IPv4 address using `/32`.
- Verified outbound Security Group traffic.
- Troubleshot an EC2 SSH timeout.
- Used `ssh -vvv` for verbose troubleshooting.
- Used `nc -vz <EC2-Public-IP> 22` to test TCP connectivity independently of SSH authentication.
- Verified the current public IP using `curl -4 ifconfig.me`.
- Identified an incorrect source IP in the Security Group.
- Corrected the Security Group rule.
- Successfully established SSH connectivity to the Ubuntu EC2 instance.
- Distinguished network connectivity problems from SSH authentication problems.
- Practiced `df -h` and `du -sh` for Linux disk usage.

---

## Troubleshooting Flow

```text
Local Mac
   ↓
Public IP Verification
   ↓
Security Group
TCP 22 → Correct Public IP /32
   ↓
Internet Gateway
   ↓
Route Table
   ↓
NACL
   ↓
EC2 Ubuntu
   ↓
SSH Service
   ↓
SSH Authentication
   ↓
Successfully Logged In
```

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

## Key Learning

Before troubleshooting the `.pem` key or username, verify:

```text
Destination IP
     ↓
Current Public IP
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

The hands-on issue was caused by an incorrect source IP in the Security Group. After correcting the source IP to the current public IPv4 `/32`, SSH connectivity succeeded.

---

## Hands-On Evidence

Screenshots are intentionally kept outside this Markdown file under:

```text
screenshots/
```

This keeps the Day-04 evidence structure consistent with the Day-03 repository layout.
