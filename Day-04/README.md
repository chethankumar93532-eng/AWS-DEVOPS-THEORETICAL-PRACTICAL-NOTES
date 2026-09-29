# Day-04 — EC2 Security Groups & SSH Troubleshooting

## 📌 Day-04 Overview

Day-04 focused on practical **AWS EC2 Security Group configuration and SSH connectivity troubleshooting**.

The hands-on work covered:

- EC2 inbound SSH access
- TCP port 22
- Security Group source IP using `/32`
- Public IP verification
- `ssh -vvv` troubleshooting
- `nc -vz` TCP connectivity testing
- Identifying an incorrect source IP in a Security Group
- Understanding the difference between network connectivity and SSH authentication
- Linux disk-usage commands: `df -h` and `du -sh`

## 🏗️ Architecture / Network Flow

```text
Local Mac
   │
   ▼
Public IP Verification
   │
   ▼
Security Group
TCP 22 → Correct Public IP /32
   │
   ▼
Internet Gateway
   │
   ▼
Route Table
   │
   ▼
NACL
   │
   ▼
EC2 Ubuntu
   │
   ▼
SSH Service
   │
   ▼
SSH Authentication
   │
   ▼
✅ Successfully Logged In
```

### Complete SSH Troubleshooting Path

```text
Client
  ↓
Internet
  ↓
Internet Gateway
  ↓
Route Table
  ↓
NACL
  ↓
Security Group
  ↓
EC2
  ↓
SSH Service
  ↓
SSH Authentication
```

## 🔐 Security Group Configuration

Inbound rule used during the hands-on:

| Setting | Value |
|---|---|
| Protocol | TCP |
| Port | 22 |
| Source | My public IPv4 address `/32` |

Outbound Security Group traffic was also verified.

## 🧪 Troubleshooting Performed

The initial SSH connection produced:

```text
ssh: connect to host <EC2-Public-IP> port 22: Operation timed out
```

The troubleshooting sequence was:

```text
SSH Timeout
    ↓
Verify EC2 Public IP
    ↓
Verify Current Public IP
    ↓
Check Security Group TCP/22
    ↓
Check Route Table
    ↓
Check NACL
    ↓
Test TCP/22 with nc
    ↓
Use ssh -vvv
    ↓
Identify incorrect source IP
    ↓
Correct Security Group /32
    ↓
Retry SSH
    ↓
✅ Successful Connection
```

## 🛠️ Commands Practiced

```bash
ssh -vvv <user>@<EC2-Public-IP>

nc -vz <EC2-Public-IP> 22

curl -4 ifconfig.me

df -h

du -sh
```

## 🧠 Key Learning

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
TCP/22 Connectivity
     ↓
SSH Authentication
```

A wrong source IP in the Security Group can result in an SSH timeout.

## 📚 Study Notes

Detailed Day-04 study/reference notes:

`SG-NACL-SSH-Troubleshooting.md`

## 📄 Original Hands-On Document

The original source document is retained separately:

`SG-NACL 4.docx`

## 🎯 Interview Focus

Be able to explain:

1. How SSH traffic reaches an EC2 instance.
2. Why TCP port 22 must be permitted by the Security Group.
3. Why using your public IPv4 address with `/32` restricts SSH access to that source address.
4. How `ssh -vvv` helps identify where SSH connectivity is failing.
5. How `nc -vz` tests TCP connectivity independently of SSH authentication.
6. The difference between a network connectivity problem and an SSH authentication problem.
7. The complete path:

```text
Client → Internet → IGW → Route Table → NACL → Security Group → EC2 → SSH
```

## 📁 Day-04 Structure

```text
Day-04/
├── README.md
├── SG-NACL-SSH-Troubleshooting.md
└── SG-NACL 4.docx
```

## ✅ Hands-On Result

```text
Local Mac
   ↓
Public IP verification
   ↓
Security Group
TCP 22 → Correct Public IP/32
   ↓
Internet Gateway
   ↓
EC2 Ubuntu
   ↓
SSH Authentication
   ↓
✅ Successfully Logged In
```
