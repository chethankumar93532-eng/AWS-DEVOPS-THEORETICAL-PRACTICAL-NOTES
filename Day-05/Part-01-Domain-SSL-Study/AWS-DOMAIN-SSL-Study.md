

# Day-05 — Domain, EC2 Web Server & SSL/TLS

## 1. WHY — Problem Solved

### Real-World Scenario

Imagine you have deployed a web application on an EC2 server.

You can access it using an IP address:

```text
http://<EC2-Public-IP>
```

But users normally need a proper domain name:

```text
https://chethandevops.xyz
```

So we need to connect:

```text
Domain
   ↓
DNS
   ↓
EC2 Server
   ↓
Web Server
   ↓
HTTPS
```

Your hands-on exercise was exactly this: you launched an EC2 server, configured the GoDaddy domain, configured DNS, deployed the web server, and worked on SSL/TLS. 

### Core Concept

> **DNS connects a human-readable domain name to a server, while SSL/TLS secures the communication between the user and that server.**

### What Problem Does DNS Solve?

Without DNS:

```text
User
 ↓
Remember EC2 Public IP
 ↓
Application
```

With DNS:

```text
User
 ↓
chethandevops.xyz
 ↓
DNS resolution
 ↓
EC2
```

The user doesn't need to remember the server's IP address.

### What Problem Does HTTPS Solve?

HTTP:

```text
Browser
   ↓
HTTP
   ↓
Web Server
```

HTTPS adds TLS encryption:

```text
Browser
   ↓
HTTPS
   ↓
Encrypted communication
   ↓
Web Server
```

### Business Value

This setup provides:

- Human-readable domain access
- DNS-based routing
- Web-server hosting
- Encrypted HTTPS communication
- Certificate-based server identity
- A production-style web access flow

---

# 2. HOW — Architecture

## Main Components

Your Day-05 setup contains:

### 1. GoDaddy

Used as the domain registrar for:

```text
chethandevops.xyz
```

### 2. Route 53

You created a hosted zone and configured DNS records for the domain. 

### 3. EC2

The EC2 instance hosts your web server/application.

### 4. Nginx

Nginx acts as the web server receiving HTTP/HTTPS requests.

### 5. Security Group

Your architecture includes inbound access for:

```text
22  → SSH
80  → HTTP
443 → HTTPS
```

The architecture reference also documents these EC2 security-group ports. 

### 6. AWS Certificate Manager

You requested a public certificate for:

```text
chethandevops.xyz
```

and selected **DNS validation**. The source records the ACM status as **Pending validation**. 

### 7. Certbot

Your hands-on notes also document attaching/configuring a third-party SSL certificate using Certbot. 

---

## Architecture

```text
                         INTERNET
                            │
                            │
                            ▼
                     ┌─────────────┐
                     │    User     │
                     │   Browser   │
                     └──────┬──────┘
                            │
                            │
                  chethandevops.xyz
                            │
                            ▼
                     ┌─────────────┐
                     │   GoDaddy   │
                     │   Domain    │
                     └──────┬──────┘
                            │
                            ▼
                     ┌─────────────┐
                     │  Route 53   │
                     │ Hosted Zone │
                     └──────┬──────┘
                            │
                       DNS Resolution
                            │
                            ▼
                    ┌────────────────┐
                    │ Internet       │
                    │ Gateway        │
                    └───────┬────────┘
                            │
                            ▼
              ┌──────────────────────────┐
              │          VPC             │
              │                          │
              │   Public Subnet          │
              │         │                │
              │         ▼                │
              │    ┌──────────┐          │
              │    │   EC2    │          │
              │    │  Nginx   │          │
              │    └────┬─────┘          │
              │         │                │
              │    Security Group        │
              │    22 / 80 / 443        │
              └─────────┬────────────────┘
                        │
                        ▼
                 SSL/TLS / HTTPS
                        │
                        ▼
                   Application
```

---

# 3. LEARN & IMPLEMENT — HANDS-ON

## Step 1 — Launch EC2

You first launched an EC2 instance to host the application/web server.

Conceptually:

```text
AWS
 ↓
VPC
 ↓
Subnet
 ↓
EC2
 ↓
Web Server
```

You then verified connectivity to the server. 

---

## Step 2 — Configure the Domain

Your domain:

```text
chethandevops.xyz
```

was purchased from GoDaddy.

The objective was:

```text
chethandevops.xyz
        ↓
AWS server
```

You configured DNS records and tested domain resolution. 

---

## Step 3 — Create Route 53 Hosted Zone

You created a Route 53 hosted zone for:

```text
chethandevops.xyz
```

The architecture reference documents records such as:

```text
@      A       EC2 Public IP
www    CNAME   @
test   CNAME   @
```



The important idea is:

```text
DNS Record
    ↓
Target
    ↓
EC2
```

---

## Step 4 — Deploy Web Server

Your EC2 instance was used as the web server and the architecture documents **Nginx** as the web server. 

The traffic path becomes:

```text
Browser
   ↓
Domain
   ↓
DNS
   ↓
EC2
   ↓
Nginx
   ↓
Website
```

---

## Step 5 — Configure Security Group

The documented inbound ports are:

```text
SSH     → 22
HTTP    → 80
HTTPS   → 443
```

Conceptually:

```text
Internet
   │
   ├── TCP 22  → SSH
   ├── TCP 80  → HTTP
   └── TCP 443 → HTTPS
```

The architecture also documents SSH as restricted to your IP while HTTP/HTTPS are used for website access. 

---

## Step 6 — Configure SSL/TLS

You requested a public certificate using AWS Certificate Manager.

Configuration:

```text
Certificate
     ↓
chethandevops.xyz
     ↓
DNS Validation
     ↓
Route 53
```

The source specifically records:

```text
ACM Certificate
Status: Pending validation
```



---

## Step 7 — DNS Validation

The validation concept is:

```text
ACM
 ↓
Provides DNS validation record
 ↓
Add DNS record
 ↓
Route 53
 ↓
ACM checks DNS ownership
 ↓
Certificate validation
```

Your architecture material describes the ACM validation record as a CNAME record added to Route 53. 

---

## Step 8 — Certbot

Your original Day-05 notes also document configuring a third-party SSL certificate using Certbot. 

So your hands-on exposure included both:

```text
Certbot
```

and:

```text
AWS Certificate Manager
```

Keep this distinction clear when discussing your actual hands-on experience.

---

# 4. HANDS-ON PROOF GATE — BREAK & FIX

This is where you should turn the domain/SSL exercise into troubleshooting experience.

## Failure 1 — Domain Doesn't Resolve

### Break

Imagine the DNS record is incorrect.

```text
chethandevops.xyz
       ↓
Wrong IP
```

### Test

Check whether the domain reaches the expected server.

Conceptually:

```text
Domain
 ↓
DNS
 ↓
Expected EC2 IP?
```

### Troubleshooting

Check:

```text
Domain
 ↓
DNS Record
 ↓
Route 53 Hosted Zone
 ↓
EC2 Public IP
```

### Fix

Correct the DNS record to the intended server address and test resolution again.

Your actual Day-05 work included configuring and testing domain resolution successfully. 

---

## Failure 2 — Website Works Over HTTP but HTTPS Doesn't

Imagine:

```text
http://chethandevops.xyz
        ↓
✅ Works

https://chethandevops.xyz
        ↓
❌ Doesn't work
```

Don't immediately blame Nginx.

Investigate:

```text
DNS
 ↓
EC2
 ↓
Security Group
 ↓
TCP 443
 ↓
Certificate
 ↓
Nginx HTTPS configuration
```

---

## Failure 3 — ACM Stays Pending Validation

Your actual Day-05 status was:

```text
ACM
 ↓
DNS Validation
 ↓
⏳ Pending Validation
```

The source states that once DNS validation completes, ACM should move to **Issued**. 

So troubleshoot:

```text
ACM
 ↓
Validation Record
 ↓
Route 53
 ↓
DNS
 ↓
Validation
```

The key question is:

> **Can ACM find the required DNS validation record?**

---

# 5. WHAT-IF — FAILURE & TROUBLESHOOTING

## Problem 1 — Domain Doesn't Reach EC2

Think layer by layer:

```text
Domain
 ↓
DNS
 ↓
Route 53
 ↓
Public IP
 ↓
Internet Gateway
 ↓
Security Group
 ↓
EC2
 ↓
Nginx
```

Don't randomly change all components.

---

## Problem 2 — HTTP Doesn't Work

Check:

```text
EC2 running?
       ↓
Security Group TCP 80?
       ↓
Nginx running?
       ↓
Nginx listening?
       ↓
Website configuration?
```

---

## Problem 3 — HTTPS Doesn't Work

Check:

```text
TCP 443 allowed?
       ↓
Certificate available?
       ↓
Certificate valid?
       ↓
Nginx configured for HTTPS?
       ↓
DNS pointing to correct server?
```

---

## Problem 4 — ACM Pending Validation

Check:

```text
Certificate
    ↓
DNS validation record
    ↓
Route 53
    ↓
Record exists?
    ↓
Correct value?
    ↓
DNS validation
```

Your source specifically records that ACM remained **Pending Validation** during this exercise. 

---

## Problem 5 — Domain Works but Certificate Warning Appears

Think:

```text
Domain
  ↓
Server
  ↓
HTTPS
  ↓
Certificate
```

A working DNS record does **not automatically mean SSL/TLS is correctly configured**.

That distinction is important:

```text
DNS Resolution ≠ SSL/TLS
```

---

# 6. SENIOR DECISION — PRODUCTION THINKING

## Domain Registrar vs DNS

Your hands-on involved both GoDaddy and Route 53.

Think of the responsibilities separately:

```text
Domain
   ↓
Registrar
   ↓
DNS Management
   ↓
Route 53
   ↓
Records
```

Don't mentally treat "domain" and "DNS" as the same thing.

---

## HTTP vs HTTPS

HTTP:

```text
Client
  ↓
HTTP :80
  ↓
Server
```

HTTPS:

```text
Client
  ↓
HTTPS :443
  ↓
TLS
  ↓
Server
```

Your Day-05 objective included understanding the difference between HTTP and HTTPS and the role SSL/TLS certificates play in encrypted communication. 

---

## ACM DNS Validation

Your hands-on used:

```text
ACM
 ↓
DNS Validation
 ↓
Route 53
 ↓
CNAME Validation Record
 ↓
Certificate Validation
```

The senior-level lesson is:

> **Certificate issuance depends on proving control of the domain, and DNS validation provides a DNS-based ownership verification mechanism.**

---

## Security Group Thinking

Your documented web-server access uses:

```text
22  → SSH
80  → HTTP
443 → HTTPS
```

For production thinking, distinguish administrative access from public application access:

```text
SSH
 ↓
Administrative access
 ↓
Restrict source

HTTP/HTTPS
 ↓
Application access
 ↓
Public web traffic
```

Your architecture documentation specifically shows SSH restricted to your IP while HTTP/HTTPS are exposed for website access. 

---

## Important Senior-Level Lesson

The biggest lesson from this exercise is:

> **A domain, DNS, web server, and SSL certificate are separate layers that must all work together.**

Think:

```text
DOMAIN
  ↓
DNS
  ↓
NETWORK
  ↓
EC2
  ↓
NGINX
  ↓
HTTP/HTTPS
  ↓
CERTIFICATE
  ↓
APPLICATION
```

If the website fails, don't troubleshoot everything simultaneously.

Find **which layer is broken first**.

---

# 7. INTERVIEW ANSWER

> **"In my AWS hands-on work, I deployed a web server on EC2 and configured a custom domain with DNS and SSL/TLS. I purchased the domain `chethandevops.xyz` from GoDaddy and configured the DNS so that the domain could resolve to my AWS server. I also created a Route 53 hosted zone and configured DNS records for the domain.**
>
> **On the AWS side, I used an EC2 instance as the web server and worked with Nginx. I configured the required security-group access, including SSH on port 22 and web access through HTTP port 80 and HTTPS port 443.**
>
> **For HTTPS, I requested a public certificate through AWS Certificate Manager and selected DNS validation. ACM provided the DNS validation information, which I started configuring through Route 53. During my hands-on exercise, the ACM certificate remained in Pending Validation, so I understood that DNS validation had not yet completed.**
>
> **I also worked with Certbot for a third-party SSL certificate, so I got practical exposure to SSL/TLS configuration outside ACM as well.**
>
> **The main thing I learned from this exercise is that domain access is a chain of multiple layers. First DNS must resolve the domain correctly, then the network path must reach the EC2 instance, the security group must allow the required port, Nginx must serve the application, and finally HTTPS requires a valid certificate and correct TLS configuration.**
>
> **From a production perspective, I would separate domain registration, DNS management, compute, web-server configuration and certificate management as individual layers. When troubleshooting, I would identify which layer is failing instead of changing multiple configurations at once."** 

---

# Commands Used / Practice Commands

Your source material documents the **configuration and architecture work**, but it does **not provide a complete command list** for the Domain/SSL exercise. So I won't invent commands and present them as commands you actually used.

The source does explicitly document:

```text
Certbot
```

as the third-party SSL configuration method used in the exercise. 

### Troubleshooting Mental Flow

```text
Domain Issue
     ↓
DNS Resolution
     ↓
Route 53
     ↓
EC2 Reachability
     ↓
Security Group
     ↓
Nginx
     ↓
Port 80 / 443
     ↓
SSL/TLS Certificate
     ↓
HTTPS
```

### Memory Hook

```text
DOMAIN
   ↓
GoDaddy
   ↓
Route 53
   ↓
DNS Resolution
   ↓
EC2
   ↓
Nginx
   ↓
ACM / Certbot
   ↓
SSL/TLS
   ↓
HTTPS
   ↓
APPLICATION
```

### One-line memory

> **Domain gives the name → DNS finds the server → EC2 runs the web server → Nginx serves the application → SSL/TLS secures HTTPS.**
