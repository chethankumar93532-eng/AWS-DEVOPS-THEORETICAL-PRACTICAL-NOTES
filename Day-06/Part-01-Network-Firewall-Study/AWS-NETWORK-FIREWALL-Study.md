# Day-06 Study Notes — AWS Network Firewall & RDP

## AWS Network Firewall
Day-06 used AWS Network Firewall as the inspection/control layer in a protected VPC.

## Architecture
```text
Client
 ↓
Internet Gateway
 ↓
GWLB Endpoint
 ↓
AWS Network Firewall
 ↓
Protected Subnet
 ↓
Windows EC2
```

## Stateful Rules
```text
SSH  → TCP 22
RDP  → TCP 3389
HTTP → TCP 80
```

## RDP Troubleshooting
The initial error was:
```text
RDP Error 0x4
Unable to connect / Configuring remote PC
```

The troubleshooting path checked EC2 health, RDP configuration, route tables, GWLB Endpoint, Network Firewall and TCP 3389.

TCP 3389 was independently tested with:
```bash
nc -vz <EC2-Public-IP> 3389
```

The source public IP in the firewall rule was incorrect. It was corrected to the current public IP using `/32`, after which RDP succeeded.

## Memory Hook
> Route the traffic through the inspection path, verify the firewall rule, independently test the port, then validate the source IP.
