# Day-06 — AWS Network Firewall & RDP

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e4e76a9f-f13d-45cb-a79e-ba100d00a25e" />


## Overview
Day-06 focused on AWS Network Firewall, protected VPC routing, GWLB Endpoint, stateful firewall rules and RDP troubleshooting for Windows Server 2025 EC2.

## 🏗️ Architecture
[Open Day-06 Architecture](architecture.md)

## 🛠️ Hands-On
[Open Day-06 Hands-On Documentation](AWS-NETWORK-FIREWALL-RDP.md)

## 📚 Study Notes
[Open Day-06 Study Notes](Part-01-Network-Firewall-Study/AWS-NETWORK-FIREWALL-Study.md)

## 📸 Hands-On Evidence

| # | Screenshot |
|---|---|
| 1 | [Open Screenshot 01](screenshots/01-day06-screenshot.png) |
| 2 | [Open Screenshot 02](screenshots/02-day06-screenshot.png) |
| 3 | [Open Screenshot 03](screenshots/03-day06-screenshot.png) |
| 4 | [Open Screenshot 04](screenshots/04-day06-screenshot.png) |
| 5 | [Open Screenshot 05](screenshots/05-day06-screenshot.png) |
| 6 | [Open Screenshot 06](screenshots/06-day06-screenshot.png) |
| 7 | [Open Screenshot 07](screenshots/07-day06-screenshot.png) |
| 8 | [Open Screenshot 08](screenshots/08-day06-screenshot.png) |
| 9 | [Open Screenshot 09](screenshots/09-day06-screenshot.png) |
| 10 | [Open Screenshot 10](screenshots/10-day06-screenshot.png) |
| 11 | [Open Screenshot 11](screenshots/11-day06-screenshot.png) |
| 12 | [Open Screenshot 12](screenshots/12-day06-screenshot.png) |
| 13 | [Open Screenshot 13](screenshots/13-day06-screenshot.png) |
| 14 | [Open Screenshot 14](screenshots/14-day06-screenshot.png) |
| 15 | [Open Screenshot 15](screenshots/15-day06-screenshot.png) |
| 16 | [Open Screenshot 16](screenshots/16-day06-screenshot.png) |
| 17 | [Open Screenshot 17](screenshots/17-day06-screenshot.png) |
| 18 | [Open Screenshot 18](screenshots/18-day06-screenshot.png) |

## Learning Flow
```text
WHY → ARCHITECTURE → HANDS-ON → BREAK/FIX → TROUBLESHOOT → SENIOR DECISION → INTERVIEW
```

## End-to-End Flow
```text
Mac → Internet → IGW → GWLB Endpoint → Network Firewall → Protected Subnet → Windows EC2 → RDP :3389
```

## Current Result
- Protected VPC architecture configured
- Firewall Subnet and Protected Subnet configured
- GWLB Endpoint configured
- Route tables configured for inspection flow
- Stateful rules configured for SSH 22, RDP 3389 and HTTP 80
- RDP Error 0x4 troubleshot
- TCP 3389 verified with `nc`
- Incorrect source public IP identified
- Current public IP `/32` configured
- RDP successfully connected
