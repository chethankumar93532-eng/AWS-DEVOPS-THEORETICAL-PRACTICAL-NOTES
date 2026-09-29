# Day-04 — Part 1: AWS Security Groups, Network ACLs & SSH Troubleshooting

## 1. WHY — Problem Solved

### Real-World Scenario

An EC2 instance is running in AWS, and an engineer needs to connect to it using SSH.

The application may be healthy, but SSH can still fail because traffic has to pass through multiple network and security layers.

The Day-04 hands-on focused on:

- EC2 Security Groups
- Inbound SSH access on TCP port 22
- Source IP restrictions using `/32`
- SSH connectivity troubleshooting
- `ssh -vvv` verbose troubleshooting
- `nc -vz` TCP connectivity testing
- Public IP verification
- Understanding the difference between network connectivity and SSH authentication
- The complete path from a local machine to an EC2 instance
- Basic Linux disk-usage commands

### Core Concept

> **Security Groups control traffic at the EC2 instance level, while Network ACLs control traffic at the subnet level; SSH troubleshooting requires checking the complete network path before troubleshooting authentication.**

### Why Is This Important?

An SSH failure does not automatically mean the `.pem` key or username is wrong.

The connection can fail before authentication because of:

```text
Wrong destination IP
        ↓
Wrong source IP
        ↓
Security Group
        ↓
Route Table
        ↓
Network ACL
        ↓
EC2
        ↓
SSH Service
        ↓
SSH Authentication
```

### Business Value

Correct network access control provides:

- Controlled administrative access
- Reduced unnecessary exposure
- Faster troubleshooting
- Least-privilege network rules
- Repeatable production troubleshooting

---

# 2. HOW — ARCHITECTURE

The Day-04 hands-on network path was:

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
   │
   ▼
Successful Login
```

### Main Components

#### EC2

The compute instance where the Ubuntu operating system and SSH service are running.

#### Security Group

The instance-level traffic control used in the hands-on.

The configured inbound rule was:

```text
Protocol: TCP
Port: 22
Source: My public IPv4 address /32
```

Outbound Security Group traffic was also verified.

#### Network ACL

A subnet-level network control that is part of the traffic path and must be considered when troubleshooting connectivity.

#### Route Table

Controls how traffic is routed from the subnet.

#### Internet Gateway

Provides the VPC's path to and from the internet for a public-subnet EC2 access scenario.

#### SSH

The remote administration protocol used in the exercise.

```text
SSH → TCP → Port 22
```

---

# 3. END-TO-END FLOW

The troubleshooting and connection flow was:

```text
1. Identify EC2 public IP
        ↓
2. Verify my current public IP
        ↓
3. Check Security Group inbound TCP/22
        ↓
4. Check route/network path
        ↓
5. Check NACL
        ↓
6. Test TCP/22 using nc
        ↓
7. Run SSH with verbose logging
        ↓
8. Identify whether failure is before authentication
        ↓
9. Correct the Security Group source IP
        ↓
10. Retry SSH
        ↓
11. Successfully connect
```

The key troubleshooting distinction is:

```text
Network Connectivity Problem
        ↓
TCP/22 cannot be reached
        ↓
Check IP / SG / Route / NACL / network path

Authentication Problem
        ↓
Network connection reaches SSH
        ↓
Then investigate username / key / permissions
```

---

# 4. LEARN & IMPLEMENT — HANDS-ON

## Step 1 — Configure SSH Access

The Security Group inbound rule used in the exercise was:

```text
Protocol: TCP
Port: 22
Source: My public IPv4 address /32
```

The `/32` notation represents a single IPv4 address.

---

## Step 2 — Verify Outbound Traffic

Outbound Security Group traffic was verified so that the EC2 instance could return traffic appropriately.

---

## Step 3 — Test SSH

The SSH connection was tested from the local Mac.

```bash
ssh -vvv <user>@<EC2-Public-IP>
```

The verbose mode helps identify where the SSH connection is failing.

---

## Step 4 — Test TCP Port 22 Independently

Instead of immediately assuming an SSH authentication problem, TCP connectivity was tested using:

```bash
nc -vz <EC2-Public-IP> 22
```

This separates basic TCP reachability from SSH authentication.

---

## Step 5 — Verify Current Public IP

The current public IPv4 address was checked from the terminal:

```bash
curl -4 ifconfig.me
```

The exercise identified that the Security Group contained an incorrect source IP.

---

## Step 6 — Correct the Security Group

The Security Group inbound rule was corrected to use the current public IPv4 address with `/32`.

```text
TCP 22
Source → Correct Public IPv4 /32
```

---

## Step 7 — Retry SSH

After correcting the Security Group rule, SSH connectivity was successfully established to the Ubuntu EC2 instance.

---

## Step 8 — Practice Linux Disk Usage

The following Linux commands were also practiced:

```bash
df -h
```

Checks filesystem disk usage and free space.

```bash
du -sh
```

Checks directory/file disk usage.

---

# 5. HANDS-ON PROOF GATE — BREAK & FIX

The Day-04 exercise included a real SSH failure and troubleshooting process.

## Failure 1 — SSH Timeout

Observed error:

```text
ssh: connect to host <EC2-Public-IP> port 22: Operation timed out
```

### Investigation

First use:

```bash
ssh -vvv <user>@<EC2-Public-IP>
```

Then test TCP connectivity:

```bash
nc -vz <EC2-Public-IP> 22
```

Then verify the current public IP:

```bash
curl -4 ifconfig.me
```

The investigation identified that the Security Group contained an incorrect source IP.

### Fix

Update the inbound rule:

```text
TCP 22
Source: Correct Public IPv4 /32
```

Then retry SSH.

### Result

```text
Local Mac
   ↓
Correct Public IP
   ↓
Security Group TCP/22
   ↓
EC2 Ubuntu
   ↓
SSH Authentication
   ↓
Successfully Logged In
```

### Lesson

> **Before troubleshooting the SSH key or username, prove that the network connection can reach TCP port 22.**

---

# 6. WHAT-IF — FAILURE & TROUBLESHOOTING

## What if SSH times out?

Check in this order:

```text
EC2 running?
     ↓
Correct public IP?
     ↓
Current client public IP?
     ↓
Security Group TCP/22?
     ↓
Route Table?
     ↓
NACL?
     ↓
TCP/22 reachable?
     ↓
SSH service?
     ↓
Authentication?
```

---

## What if the Security Group rule looks correct?

Verify that the source IP is your current public IP:

```bash
curl -4 ifconfig.me
```

Compare that address with the Security Group `/32`.

---

## What if TCP/22 is unreachable?

Use:

```bash
nc -vz <EC2-Public-IP> 22
```

If TCP/22 cannot be reached, investigate the network path before troubleshooting the `.pem` key.

---

## What if TCP/22 is reachable but SSH still fails?

Then move to SSH authentication troubleshooting:

```text
TCP connectivity works
        ↓
SSH service reachable
        ↓
Check username
        ↓
Check key
        ↓
Check key permissions
        ↓
Check authentication
```

---

## What if the instance is not publicly reachable?

Verify the EC2 public IP and the network path. A private instance requires an appropriate administrative access design rather than direct internet SSH.

---

# 7. SENIOR DECISION LAYER

## Security Group Access

For administrative SSH access, the hands-on used:

```text
TCP 22
Source: Specific Public IPv4 /32
```

This is more restrictive than allowing SSH from every IPv4 address.

Avoid unnecessary broad administrative access.

---

## SG vs NACL

```text
Security Group
      ↓
Instance level
      ↓
Stateful

Network ACL
      ↓
Subnet level
      ↓
Stateless
```

During troubleshooting, understand that both can participate in the traffic path.

---

## Production SSH Thinking

A production environment should consider:

- Least-privilege access
- Restricted source addresses
- Avoiding unnecessary public exposure
- Consistent network rules
- Logging and monitoring
- A controlled administrative access path

The Day-04 hands-on specifically demonstrated why a changing public IP can cause an SSH rule using `/32` to stop matching.

---

# 8. INTERVIEW ANSWER

> **“Tell me about your experience troubleshooting SSH connectivity to EC2.”**

In my hands-on AWS work, I configured an EC2 Security Group to allow SSH over TCP port 22 from my public IPv4 address using a `/32` source restriction.

I then tested SSH connectivity from my local Mac. I encountered an SSH timeout where the connection to port 22 was not completing. Instead of immediately troubleshooting the `.pem` key or username, I first checked the network path.

I used `ssh -vvv` for verbose SSH troubleshooting and `nc -vz <EC2-Public-IP> 22` to test TCP port 22 independently of SSH authentication. I also used `curl -4 ifconfig.me` to verify my current public IP.

I found that the Security Group contained an incorrect source IP. I corrected the inbound TCP/22 rule to use the correct public IPv4 address with `/32`, and SSH connectivity was successfully established to the Ubuntu EC2 instance.

The main lesson for me was to separate network connectivity from SSH authentication. My troubleshooting path is to verify the destination IP, current client IP, Security Group, route table, NACL, TCP/22 connectivity, SSH service, and only then investigate authentication.

---

# 9. COMMANDS USED / PRACTICE COMMANDS

```bash
# SSH with verbose troubleshooting
ssh -vvv <user>@<EC2-Public-IP>

# Test TCP port 22
nc -vz <EC2-Public-IP> 22

# Check current public IPv4
curl -4 ifconfig.me

# Check filesystem disk usage
df -h

# Check directory/file disk usage
du -sh
```

---

# 10. HANDS-ON EVIDENCE

The Day-04 source records the following hands-on sequence:

```text
Security Group
    ↓
TCP 22
    ↓
Incorrect source IP discovered
    ↓
Current public IP verified
    ↓
Security Group corrected
    ↓
TCP connectivity tested
    ↓
SSH successfully established
```

Screenshots should be stored separately under:

```text
screenshots/
```

and referenced from the hands-on evidence file.

---

# 🧠 DAY-04 MEMORY HOOK

```text
SSH Failure
     ↓
Correct EC2 Public IP?
     ↓
Correct Client Public IP?
     ↓
Security Group TCP/22?
     ↓
Route Table?
     ↓
NACL?
     ↓
nc -vz TCP Test
     ↓
ssh -vvv
     ↓
Authentication
     ↓
Success
```

**One-line memory:**

> **“For an EC2 SSH timeout, prove network reachability first; verify the destination IP, source IP, SG, route, NACL and TCP/22 before troubleshooting SSH authentication.”**
