# Day-04 — EC2 Security Groups & SSH Troubleshooting

## Topics & Activities Completed

Worked with AWS EC2 Security Groups and configured inbound SSH access.

Understood how SSH (TCP port 22) traffic flows from a local machine to an EC2 instance.

Configured an inbound Security Group rule:

- Protocol: TCP
- Port: 22
- Source: My public IPv4 address `/32`

Verified outbound Security Group traffic.

## SSH Connectivity Troubleshooting

Troubleshot an EC2 SSH connection issue where the connection was showing:

```text
ssh: connect to host <EC2-Public-IP> port 22: Operation timed out
```

Used:

```bash
ssh -vvv <user>@<EC2-Public-IP>
```

to perform verbose SSH troubleshooting and identify that the issue was occurring before authentication.

Used:

```bash
nc -vz <EC2-Public-IP> 22
```

to test TCP port 22 connectivity independently of SSH authentication.

Verified my public IP address from the terminal and identified that I had configured an incorrect source IP in the Security Group.

Used:

```bash
curl -4 ifconfig.me
```

to verify the public IP.

Corrected the Security Group rule with the correct public IP `/32`.

Successfully established SSH connectivity to the Ubuntu EC2 instance.

## Network Connectivity vs SSH Authentication

Learned the difference between:

- Network connectivity problems
- SSH authentication problems

An SSH timeout should be investigated through the complete network path before focusing on the `.pem` key or username.

## Complete Network Path

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

## Troubleshooting Flow

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
Test TCP/22
    ↓
Use ssh -vvv
    ↓
Identify incorrect source IP
    ↓
Correct Security Group /32
    ↓
Retry SSH
    ↓
Successful SSH Connection
```

## Linux Disk Commands Practiced

### `df -h`

Used for checking filesystem disk usage and available space.

```bash
df -h
```

### `du -sh`

Used for checking directory/file disk usage.

```bash
du -sh
```

### Public IP Verification

```bash
curl -4 ifconfig.me
```

## Key Learning

Today I gained practical experience troubleshooting EC2 SSH connectivity.

I learned that before troubleshooting the `.pem` key or username, I should first verify:

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

I also understood how a wrong source IP in a Security Group can result in an SSH timeout.

## Hands-On Result

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

## Day-04 Update

Worked on EC2 Security Groups and SSH connectivity. Configured inbound SSH access on TCP port 22 using my public IP `/32`. Troubleshot an SSH timeout using `ssh -vvv` and `nc -vz`, verified my public IP, and identified that I had configured the wrong source IP in the Security Group. Corrected the IP and successfully connected to the Ubuntu EC2 instance. Also practiced `df -h` and `du -sh` for Linux disk usage monitoring. Learned practical troubleshooting of SSH connectivity across SG, NACL, routing, IGW, and EC2 layers.

## FYR Screenshots

Screenshots/evidence can be kept separately under a Day-04 screenshots folder when available.
