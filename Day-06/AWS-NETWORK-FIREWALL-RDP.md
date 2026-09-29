# Day-06 Hands-On — AWS Network Firewall & RDP

## Objective
Build and understand a protected VPC traffic-inspection path using AWS Network Firewall, Firewall Subnet, Protected Subnet, GWLB Endpoint and route tables, then troubleshoot RDP access to Windows Server 2025 EC2.

## 1. Architecture Components
- Protected VPC
- Firewall Subnet
- Protected Subnet
- Gateway Load Balancer (GWLB) Endpoint
- AWS Network Firewall
- Route tables
- Windows Server 2025 EC2

## 2. Stateful Rules
| Protocol | Port | Purpose |
|---|---:|---|
| SSH | TCP 22 | SSH access |
| RDP | TCP 3389 | Windows remote access |
| HTTP | TCP 80 | HTTP traffic |

## 3. RDP Failure
Initial RDP error:

```text
RDP Error 0x4
Unable to connect / Configuring remote PC
```

## 4. Troubleshooting
Checked:
```text
EC2 status
→ RDP configuration
→ Route tables
→ GWLB Endpoint
→ Network Firewall
→ TCP 3389
```

TCP 3389 was independently tested with:

```bash
nc -vz <EC2-Public-IP> 3389
```

The port was reachable.

## 5. Root Cause and Fix
The Network Firewall RDP rule had the wrong source public IP.

It was corrected to:

```text
CURRENT_PUBLIC_IP/32
```

After the rule update, RDP successfully connected to the Windows EC2 instance.

## 6. Final Flow
```text
Mac
 → Internet
 → Internet Gateway
 → GWLB Endpoint
 → AWS Network Firewall
 → Protected Subnet
 → Windows Server 2025 EC2
 → RDP :3389
```

## 7. Commands Used / Practice Commands
```bash
nc -vz <EC2-Public-IP> 3389
```

No additional command transcript is invented because it is not present in the source.

## 8. Hands-On Evidence
- [01-day06-screenshot](screenshots/01-day06-screenshot.png)
- [02-day06-screenshot](screenshots/02-day06-screenshot.png)
- [03-day06-screenshot](screenshots/03-day06-screenshot.png)
- [04-day06-screenshot](screenshots/04-day06-screenshot.png)
- [05-day06-screenshot](screenshots/05-day06-screenshot.png)
- [06-day06-screenshot](screenshots/06-day06-screenshot.png)
- [07-day06-screenshot](screenshots/07-day06-screenshot.png)
- [08-day06-screenshot](screenshots/08-day06-screenshot.png)
- [09-day06-screenshot](screenshots/09-day06-screenshot.png)
- [10-day06-screenshot](screenshots/10-day06-screenshot.png)
- [11-day06-screenshot](screenshots/11-day06-screenshot.png)
- [12-day06-screenshot](screenshots/12-day06-screenshot.png)
- [13-day06-screenshot](screenshots/13-day06-screenshot.png)
- [14-day06-screenshot](screenshots/14-day06-screenshot.png)
- [15-day06-screenshot](screenshots/15-day06-screenshot.png)
- [16-day06-screenshot](screenshots/16-day06-screenshot.png)
- [17-day06-screenshot](screenshots/17-day06-screenshot.png)
- [18-day06-screenshot](screenshots/18-day06-screenshot.png)

## Key Learning
The main troubleshooting lesson was to validate the complete network path and verify the actual client source IP before changing application settings.
