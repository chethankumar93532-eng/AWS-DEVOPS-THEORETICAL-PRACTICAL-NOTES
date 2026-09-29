# Day-05 — Domain, EC2 Server Deployment, Route 53 & SSL/TLS

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/9cdd675a-a843-40df-96fc-19cca23233e8" />


## Overview

Day-05 focused on deploying a web/application server on EC2, connecting a GoDaddy domain through DNS, configuring Route 53, using Nginx as the web server, and working through SSL/TLS configuration with AWS Certificate Manager.

## What You Will Learn

- EC2 server deployment
- Domain configuration with GoDaddy
- Route 53 hosted zones and DNS records
- Nginx/web server flow
- HTTP vs HTTPS
- SSL/TLS fundamentals
- ACM certificate request
- DNS validation
- Troubleshooting a certificate in Pending Validation
- End-to-end domain → DNS → EC2 → HTTPS flow

## 🏗️ Architecture

[Open Day-05 Architecture](architecture.md)

## 🛠️ Hands-On

[Open Day-05 Hands-On Documentation](AWS-DOMAIN-SERVER-DEPLOYMENT.md)

## 📚 Study Notes

[Open Day-05 Study Notes](Part-01-Domain-SSL-Study/AWS-DOMAIN-SSL-Study.md)

## 📸 Hands-On Evidence

| Screenshot | Direct Link |
|---|---|
| Screenshot 01 | [Open](screenshots/01-day05-screenshot.png) |
| Screenshot 02 | [Open](screenshots/02-day05-screenshot.png) |
| Screenshot 03 | [Open](screenshots/03-day05-screenshot.png) |
| Screenshot 04 | [Open](screenshots/04-day05-screenshot.png) |
| Screenshot 05 | [Open](screenshots/05-day05-screenshot.png) |
| Screenshot 06 | [Open](screenshots/06-day05-screenshot.png) |
| Screenshot 07 | [Open](screenshots/07-day05-screenshot.png) |
| Screenshot 08 | [Open](screenshots/08-day05-screenshot.png) |
| Screenshot 09 | [Open](screenshots/09-day05-screenshot.png) |

## Learning Flow

```text
WHY
 ↓
Architecture
 ↓
Hands-On
 ↓
SSL/DNS Troubleshooting
 ↓
Study Notes
 ↓
Hands-On Evidence
```

## End-to-End Flow

```text
User
 ↓
chethandevops.xyz
 ↓
GoDaddy
 ↓
Route 53
 ↓
AWS EC2
 ↓
Nginx
 ↓
SSL/TLS
 ↓
HTTPS
 ↓
Application
```

## Current Status

- EC2 server: Launched
- GoDaddy domain: Configured
- DNS resolution: Configured/Tested
- Route 53 hosted zone: Created
- DNS records: Created
- ACM certificate: **Pending validation**
- HTTPS completion: Pending certificate validation

## Key Learning

The Day-05 exercise connected domain registration, DNS, EC2 web-server deployment, and SSL/TLS into one end-to-end deployment flow.

## Repository Structure

```text
Day-05/
├── README.md
├── architecture.md
├── AWS-DOMAIN-SERVER-DEPLOYMENT.md
├── screenshots/
│   ├── 01-day05-screenshot.png
│   ├── 02-day05-screenshot.png
│   ├── 03-day05-screenshot.png
│   ├── 04-day05-screenshot.png
│   ├── 05-day05-screenshot.png
│   ├── 06-day05-screenshot.png
│   ├── 07-day05-screenshot.png
│   ├── 08-day05-screenshot.png
│   └── 09-day05-screenshot.png
└── Part-01-Domain-SSL-Study/
    └── AWS-DOMAIN-SSL-Study.md
```
