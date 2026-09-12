Absolutely bro 👍 Here is **Day-02 only**, kept completely separate so you can directly copy and paste it into:

```text
Day-02/AWS-VPC-EC2-Networking.md
```

```markdown
# Day 02 — AWS VPC & EC2 Networking

## Topics Covered

- Public Subnet vs Private Subnet
- NAT Gateway
- Elastic IP
- Security Groups
- Bastion Host
- Private EC2
- SSH Connectivity
- VPC DNS
- EC2 Networking
- Linux Network Troubleshooting
- AWS Network Troubleshooting
- Internet Connectivity from Private EC2

---

# 1. Public Subnet vs Private Subnet

## Scenario

Imagine a company is deploying a three-tier application:

```text
Users
  |
  ↓
Web Layer
  |
  ↓
Application Layer
  |
  ↓
Database Layer
```

The Web Layer may need to receive requests from the internet.

The Application and Database layers should not be directly exposed to the internet.

So we separate the workloads into different subnets.

```text
                    INTERNET
                       |
                       ↓
                 PUBLIC SUBNET
                       |
                  Web / Bastion
                       |
                       ↓
                PRIVATE SUBNET
                       |
                 Application
                       |
                       ↓
                PRIVATE SUBNET
                       |
                    Database
```

---

## Core Concept

A public subnet has a route to an Internet Gateway, while a private subnet does not have a direct route to an Internet Gateway for internet access.

### Simple way to remember

> Public subnet = Internet Gateway route exists.

> Private subnet = No direct Internet Gateway route.

---

## Why

Subnet separation provides:

- Network segmentation
- Reduced attack surface
- Controlled traffic flow
- Better security
- Different routing policies
- Separation of workloads

For example:

```text
Public:
Load Balancer
Bastion Host

Private:
Application Servers
Database
```

This follows the principle:

> Keep workloads private unless they actually need public access.

---

## Components

```text
VPC
 |
 +---- Public Subnet
 |       |
 |       +---- Load Balancer
 |       +---- Bastion
 |
 +---- Private Subnet
         |
         +---- Application EC2
         +---- Database
```

The components work together through:

- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Private IP addresses

---

## End-to-End Flow

### Public resource

```text
Internet
   |
   ↓
Internet Gateway
   |
   ↓
Public Route Table
   |
   ↓
Public Subnet
   |
   ↓
Resource
```

### Private resource communicating internally

```text
Private EC2
   |
   ↓
Private Subnet
   |
   ↓
Private Route Table
   |
   ↓
Internal AWS Resource
```

### Private resource accessing internet

```text
Private EC2
   |
   ↓
Private Route Table
   |
   ↓
NAT Gateway
   |
   ↓
Public Route Table
   |
   ↓
Internet Gateway
   |
   ↓
Internet
```

---

## Practical

Example VPC:

```text
VPC:
10.0.0.0/16
```

Subnets:

```text
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

Public route:

```text
10.0.0.0/16 → local
0.0.0.0/0   → Internet Gateway
```

Private route:

```text
10.0.0.0/16 → local
0.0.0.0/0   → NAT Gateway
```

---

## Senior-Level Understanding

A common beginner statement is:

> "A subnet is public because the EC2 has a public IP."

That is not the correct architectural definition.

The subnet's routing configuration is what makes it public or private.

Also:

> Private does not mean completely disconnected from the internet.

A private resource can have outbound internet access through a NAT Gateway.

The important distinction is:

```text
Public:
Direct internet path through IGW

Private:
No direct internet path through IGW
```

---

## Interview Answer

"I separate public and private subnets based primarily on routing and workload exposure. A public subnet has a route to an Internet Gateway, while a private subnet does not have a direct internet route through the IGW. I typically place internet-facing components such as load balancers in public subnets and application or database workloads in private subnets. If private workloads need outbound internet access, I use a NAT Gateway. This reduces public exposure and gives me better control over the architecture."


---

# 2. NAT Gateway

## Scenario

We have a private EC2 instance.

It does not have a public IP because we don't want it directly exposed to the internet.

However, the server needs to install packages:

```bash
sudo apt update
```

or:

```bash
sudo apt install nginx
```

The private EC2 needs outbound internet access.

How can it access the internet without becoming public?

Through a NAT Gateway.

---

## Core Concept

A NAT Gateway allows resources in a private subnet to initiate outbound connections to the internet without giving those resources public IP addresses.

### Simple way to remember

> NAT Gateway = Private resources → Internet outbound access.

---

## Why

Without NAT:

```text
Private EC2
     |
     X
  Internet
```

We might be tempted to give the EC2 a public IP.

That increases its exposure.

Instead:

```text
Private EC2
     |
     ↓
NAT Gateway
     |
     ↓
Internet Gateway
     |
     ↓
Internet
```

This allows the private server to access external resources while keeping it without a direct public internet path.

---

## Components

```text
Private EC2
     |
     ↓
Private Route Table
     |
     ↓
NAT Gateway
     |
     ↓
Public Subnet
     |
     ↓
Public Route Table
     |
     ↓
Internet Gateway
     |
     ↓
Internet
```

---

## End-to-End Flow

Suppose the private EC2 runs:

```bash
sudo apt update
```

The traffic follows:

```text
Private EC2
     |
     ↓
Private Route Table
     |
     ↓
0.0.0.0/0 → NAT Gateway
     |
     ↓
NAT Gateway
     |
     ↓
Public Subnet
     |
     ↓
Public Route Table
     |
     ↓
Internet Gateway
     |
     ↓
Internet
```

The NAT Gateway performs network address translation for the outbound traffic.

The destination sees the NAT Gateway's public address rather than the private EC2's private address.

---

## Practical

Private route table:

```text
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

NAT Gateway should be placed in a public subnet.

The public subnet's route table needs:

```text
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Test from the private EC2:

```bash
sudo apt update
```

Test DNS:

```bash
nslookup google.com
```

Test HTTPS:

```bash
curl -I https://google.com
```

---

## Senior-Level Understanding

NAT Gateway is primarily about connectivity.

It should not be treated as the complete security layer.

Senior engineers consider:

- NAT Gateway cost
- Multi-AZ design
- Cross-AZ traffic
- High availability
- VPC endpoints
- Egress restrictions

For production environments, a common design is to deploy NAT Gateway per Availability Zone when required for resilience and to avoid unnecessary cross-AZ traffic.

For AWS service access, VPC endpoints may sometimes be preferable to sending traffic through NAT.

---

## Interview Answer

"I use a NAT Gateway when private resources need outbound internet access without exposing those resources with public IP addresses. The private subnet's default route points to the NAT Gateway. The NAT Gateway is deployed in a public subnet, and that subnet routes internet traffic through the Internet Gateway. This allows private EC2 instances to perform activities such as package downloads while remaining private. At a senior level, I also consider NAT cost, Availability Zone resilience, cross-AZ traffic and whether VPC endpoints can reduce unnecessary NAT usage."


---

# 3. Elastic IP

## Scenario

Suppose a system requires a stable public IPv4 address.

For example, an external system may allow traffic only from a specific IP:

```text
Allowed IP:
203.0.113.10
```

If the public IP changes, communication may stop.

We need a persistent public IPv4 address.

---

## Core Concept

An Elastic IP is a persistent public IPv4 address that can be associated with an AWS resource.

### Simple way to remember

> Elastic IP = Persistent public IPv4 address.

---

## Why

Elastic IPs can be useful when infrastructure requires a stable public IPv4 address.

Possible examples:

- NAT Gateway
- Specific infrastructure
- Legacy applications
- IP allowlisting requirements

---

## Components

```text
Elastic IP
    |
    +---- NAT Gateway
```

or, when genuinely required:

```text
Elastic IP
    |
    +---- EC2
```

---

## End-to-End Flow

For a NAT Gateway:

```text
Private EC2
    |
    ↓
NAT Gateway
    |
    ↓
Elastic IP
    |
    ↓
Internet
```

External systems see the NAT Gateway's public address.

---

## Practical

Using AWS Console:

```text
EC2
  ↓
Elastic IPs
  ↓
Allocate Elastic IP
  ↓
Associate
```

AWS CLI example:

```bash
aws ec2 allocate-address --domain vpc
```

---

## Senior-Level Understanding

Do not assign Elastic IPs to every EC2 instance.

First ask:

> Does this workload really require a static public IP?

A better production architecture is often:

```text
Internet
   |
   ↓
Load Balancer
   |
   ↓
Private EC2
```

instead of:

```text
Internet
   |
   ↓
Public EC2
```

Public exposure should be minimized.

---

## Interview Answer

"An Elastic IP is a persistent public IPv4 address that can be associated with an AWS resource. I use it only when a stable public IP is genuinely required, such as for a NAT Gateway or specific allowlisting requirements. I wouldn't assign Elastic IPs to every application server because that increases public exposure. Where possible, I'd place application instances behind a load balancer and keep them private."


---

# 4. Security Groups

## Scenario

An EC2 instance is running.

Someone tries to connect:

```text
TCP 22
SSH
```

Should AWS allow the connection?

The Security Group determines whether the traffic is permitted.

---

## Core Concept

A Security Group is a stateful virtual firewall that controls allowed inbound and outbound traffic for AWS resources.

### Simple way to remember

> Security Group = Traffic access control.

---

## Why

Security Groups protect workloads by controlling network access.

They help prevent:

- Unauthorized access
- Unnecessary exposure
- Accidental public access
- Unwanted network communication

For example, an application server may need:

```text
HTTP → Load Balancer
SSH → Bastion
Database → Database Server
```

but it should not accept SSH from the entire internet.

---

## Components

Example:

```text
Security Group

Inbound Rules

SSH
TCP 22
Source: Trusted IP

HTTP
TCP 80
Source: Load Balancer SG
```

Outbound rules control traffic leaving the resource.

---

## End-to-End Flow

Suppose you connect to EC2 using SSH:

```text
Laptop
   |
   ↓
Internet
   |
   ↓
Internet Gateway
   |
   ↓
Route Table
   |
   ↓
EC2 Security Group
   |
   ↓
TCP 22 allowed?
   |
   +---- YES → EC2
   |
   └---- NO → Connection blocked
```

---

## Practical

Example SSH rule:

```text
Protocol: TCP
Port: 22
Source: Your trusted IP
```

Then:

```bash
chmod 400 key.pem
```

Connect:

```bash
ssh -i key.pem ubuntu@<public-ip>
```

---

## Senior-Level Understanding

Security Groups are **stateful**.

This means return traffic for an allowed connection is automatically handled according to the established connection state.

A common mistake is:

```text
SSH
0.0.0.0/0
```

This means SSH is exposed to the entire internet.

A better approach is:

```text
SSH
Source: Trusted IP
```

or, for internal architecture:

```text
Application EC2
SSH
Source: Bastion Security Group
```

Security Groups also support referencing another Security Group as a source, which is extremely useful for service-to-service access.

Remember:

```text
Route Table
→ Determines the path

Security Group
→ Determines whether traffic is allowed
```

---

## Interview Answer

"A Security Group is a stateful virtual firewall associated with AWS resources such as EC2. It controls allowed inbound and outbound traffic. For example, instead of allowing SSH from the entire internet, I would restrict it to a trusted source or use a controlled management mechanism. In a multi-tier architecture, I can also reference Security Groups rather than hard-coding IP addresses, such as allowing the application Security Group to communicate with the database Security Group."


---

# 5. Bastion Host

## Scenario

We have an EC2 instance in a private subnet.

It has:

```text
Private IP: 10.0.2.10
Public IP: None
```

An administrator needs to access it.

We don't want to give the private EC2 a public IP.

A Bastion Host can act as a controlled entry point.

---

## Core Concept

A Bastion Host is a controlled server in a public network used to provide administrative access to private resources.

### Simple way to remember

> Bastion = Controlled entry point to private servers.

---

## Why

Without a Bastion:

```text
Internet
   |
   +---- Private EC2
   +---- Private EC2
   +---- Private EC2
```

We might expose multiple systems.

With a Bastion:

```text
Internet
   |
   ↓
Bastion
   |
   ↓
Private Network
   |
   +---- Private EC2
   +---- Private EC2
```

Only the Bastion needs the controlled administrative entry point.

---

## Components

```text
Administrator Laptop
       |
       ↓
Internet
       |
       ↓
Internet Gateway
       |
       ↓
Public Subnet
       |
       ↓
Bastion Host
       |
       ↓
Private Subnet
       |
       ↓
Private EC2
```

---

## End-to-End Flow

First connect to Bastion:

```bash
ssh -i bastion.pem ubuntu@<bastion-public-ip>
```

Then connect to private EC2:

```bash
ssh -i private-key.pem ubuntu@<private-ip>
```

Flow:

```text
Laptop
   |
   | SSH
   ↓
Bastion
   |
   | SSH
   ↓
Private EC2
```

---

## Practical

Bastion Security Group:

```text
Inbound:

SSH
TCP 22
Source: Trusted Administrator IP
```

Private EC2 Security Group:

```text
Inbound:

SSH
TCP 22
Source: Bastion Security Group
```

This is much better than:

```text
SSH
0.0.0.0/0
```

on the private EC2.

---

## Senior-Level Understanding

A Bastion Host itself becomes infrastructure that must be:

- Patched
- Secured
- Monitored
- Audited
- Access controlled

Therefore, a senior engineer should ask:

> Do we actually need a Bastion?

In AWS environments, Systems Manager Session Manager can often provide administrative access without exposing SSH publicly.

The architectural goal is not:

> "Always use Bastion."

The goal is:

> "Provide controlled administrative access with the minimum required exposure."

---

## Interview Answer

"A Bastion Host is a controlled administrative entry point into private infrastructure. Instead of giving every private EC2 instance a public IP, I allow administrators to connect to the Bastion and then restrict SSH access from the Bastion's Security Group to the private instances. However, Bastions add operational and security overhead, so in AWS I would evaluate Systems Manager Session Manager as an alternative where appropriate."


---

# 6. Private EC2

## Scenario

An application server does not need to receive requests directly from the internet.

Giving it a public IP increases unnecessary exposure.

Therefore, we place it in a private subnet.

---

## Core Concept

A private EC2 is an EC2 instance deployed without direct public internet exposure and accessed through controlled private networking.

---

## Why

Private EC2 instances are useful for:

- Application servers
- Internal APIs
- Background workers
- Internal services
- Database-related workloads

They reduce unnecessary public exposure.

---

## Components

```text
VPC
 |
Private Subnet
 |
Private Route Table
 |
Private EC2
```

If outbound internet is required:

```text
Private EC2
 |
Private Route Table
 |
NAT Gateway
 |
Internet Gateway
 |
Internet
```

---

## End-to-End Flow

### Internal communication

```text
Application EC2
       |
       ↓
Private IP
       |
       ↓
Database EC2 / RDS
```

### Outbound internet

```text
Private EC2
       |
       ↓
Private Route Table
       |
       ↓
NAT Gateway
       |
       ↓
Internet Gateway
       |
       ↓
Internet
```

### Administrative access

```text
Administrator
       |
       ↓
Bastion / Systems Manager
       |
       ↓
Private EC2
```

---

## Practical

Private EC2:

```text
Private IP:
YES

Public IP:
NO
```

Private route table:

```text
10.0.0.0/16 → local
0.0.0.0/0   → NAT Gateway
```

---

## Senior-Level Understanding

Private does not mean:

> "This server cannot communicate with anything."

It means:

> "The server is not directly exposed to the public internet."

A private EC2 can communicate with:

- Other VPC resources
- Databases
- Internal services
- AWS services
- Internet through NAT
- On-premises networks through VPN or Direct Connect

The goal is:

> Controlled connectivity, not zero connectivity.

---

## Interview Answer

"I use private EC2 instances for workloads that don't require direct internet exposure, such as application servers and internal services. They communicate using private IP addresses and can receive administrative access through a Bastion or Systems Manager. If they require outbound internet access, I provide that through a NAT Gateway rather than assigning public IP addresses. This reduces the attack surface while still allowing required connectivity."


---

# 7. SSH Connectivity

## Scenario

An administrator needs to access a Linux EC2 instance remotely.

For example:

```bash
sudo apt update
```

or:

```bash
sudo systemctl status nginx
```

SSH provides the remote administrative connection.

---

## Core Concept

SSH (Secure Shell) is a secure protocol used to remotely access and administer systems.

---

## Why

SSH allows administrators to:

- Execute commands
- Install software
- Troubleshoot
- Read logs
- Manage services
- Configure applications

---

## Components

```text
Administrator Laptop
       |
       ↓
Network
       |
       ↓
Route
       |
       ↓
Security Group
       |
       ↓
EC2
       |
       ↓
SSH Service
```

---

## End-to-End Flow

### Public EC2

```text
Laptop
 |
 ↓
Internet
 |
 ↓
Internet Gateway
 |
 ↓
Public Route Table
 |
 ↓
Public Subnet
 |
 ↓
EC2
```

### Private EC2 through Bastion

```text
Laptop
 |
 ↓
Bastion
 |
 ↓
Private Network
 |
 ↓
Private EC2
```

---

## Practical

Set private key permissions:

```bash
chmod 400 key.pem
```

Connect:

```bash
ssh -i key.pem ubuntu@<public-ip>
```

Check SSH service:

```bash
sudo systemctl status ssh
```

Check the server's IP:

```bash
ip addr
```

Check routes:

```bash
ip route
```

---

## Senior-Level Understanding

When SSH fails, don't immediately assume:

> "The key is wrong."

SSH connectivity depends on multiple layers.

Check:

```text
EC2 running?
       ↓
Correct IP?
       ↓
Correct username?
       ↓
Correct key?
       ↓
Key permissions?
       ↓
Route Table?
       ↓
Security Group?
       ↓
Network connectivity?
       ↓
SSH service?
```

This systematic approach is much better than changing random configurations.

---

## Interview Answer

"When troubleshooting SSH connectivity, I validate the complete network and application path rather than checking only the SSH key. I first verify that the EC2 is running and that I'm using the correct IP address and username. Then I validate key permissions, routing and Security Group rules. Finally, I check whether the SSH service is running on the instance. For private instances, I also verify the administrative path through a Bastion or Systems Manager."


---

# 8. VPC DNS

## Scenario

Suppose an application needs to communicate with a database.

Using an IP address directly:

```text
10.0.2.15
```

creates a dependency on that address.

Instead, we can use a DNS name:

```text
database.internal
```

The application doesn't need to know the database's current IP address.

---

## Core Concept

DNS translates human-readable names into IP addresses and provides a naming layer for network communication.

---

## Why

DNS provides:

- Service discovery
- Stable naming
- Load balancing
- Failover
- Internal communication
- Public name resolution

It reduces dependency on hard-coded IP addresses.

---

## Components

AWS networking can involve:

- VPC DNS resolution
- VPC DNS hostnames
- Route 53
- Private Hosted Zones
- Public DNS

---

## End-to-End Flow

```text
Application
    |
    ↓
DNS Query
    |
    ↓
DNS Resolver
    |
    ↓
IP Address
    |
    ↓
Application connects
```

Example:

```text
Application
    |
    ↓
database.internal
    |
    ↓
10.0.2.15
    |
    ↓
Database
```

---

## Practical

Check DNS resolution from Linux:

```bash
nslookup google.com
```

or:

```bash
dig google.com
```

Test connectivity:

```bash
curl -I https://google.com
```

---

## Senior-Level Understanding

DNS is more than:

> "Converting names to IP addresses."

It provides an abstraction between the application and infrastructure.

```text
Application
     |
     ↓
DNS Name
     |
     ↓
Infrastructure
```

Infrastructure can change while the application continues using the same name.

This becomes especially important in:

- Microservices
- Load balancing
- Kubernetes
- Auto Scaling
- Disaster recovery
- Multi-region architectures

---

## Interview Answer

"DNS provides a naming abstraction between applications and infrastructure. Instead of applications depending directly on IP addresses, they can communicate using DNS names. In AWS, VPC DNS capabilities and Route 53 can provide internal and external name resolution. This becomes particularly important in dynamic environments where instances can be replaced or scaled, because the application doesn't need to know the underlying infrastructure IP addresses."


---

# 9. Network Troubleshooting

## Scenario

Our private EC2 runs:

```bash
sudo apt update
```

but it fails to reach package repositories.

Instead of randomly changing AWS settings, we need to determine where the network path is failing.

---

## Core Concept

Network troubleshooting is the systematic process of identifying where communication fails between a source and destination.

### Simple way to remember

> Follow the packet.

---

## Why

Connectivity can fail at many different layers:

- EC2
- Network interface
- IP addressing
- Subnet
- Route Table
- NAT Gateway
- Internet Gateway
- Security Group
- DNS
- Operating System
- Application

Changing random settings can make troubleshooting harder.

---

## Components

The troubleshooting path should be:

```text
Source
  |
  ↓
IP Address
  |
  ↓
Subnet
  |
  ↓
Route Table
  |
  ↓
Gateway
  |
  ↓
Destination
  |
  ↓
Security
  |
  ↓
Service
```

---

## End-to-End Flow

For private EC2 internet access:

```text
Private EC2
     |
     ↓
Private Subnet
     |
     ↓
Private Route Table
     |
     ↓
0.0.0.0/0
     |
     ↓
NAT Gateway
     |
     ↓
Public Subnet
     |
     ↓
Public Route Table
     |
     ↓
Internet Gateway
     |
     ↓
Internet
```

Every stage must be correctly configured.

---

## Practical Commands

### Check IP address

```bash
ip addr
```

This tells us the network interfaces and IP addresses configured on the server.

---

### Check routing

```bash
ip route
```

This shows the Linux routing table.

Example:

```text
default via 10.0.2.1 dev eth0
10.0.0.0/16 dev eth0
```

---

### Test basic connectivity

```bash
ping <ip-address>
```

Note:

> Ping failure does not always mean the network is completely unavailable because ICMP may be blocked.

---

### Test DNS

```bash
nslookup google.com
```

or:

```bash
dig google.com
```

---

### Test HTTPS

```bash
curl -I https://google.com
```

This is often more useful than ping when testing web connectivity.

---

### Check SSH service

```bash
sudo systemctl status ssh
```

---

### Test package repository connectivity

```bash
sudo apt update
```

---

## Decision Tree

```text
Cannot connect
      |
      ↓
Is EC2 running?
      |
      ↓
Correct IP?
      |
      ↓
Correct subnet?
      |
      ↓
Correct route table?
      |
      ↓
Correct gateway?
      |
      ↓
NAT/IGW configured?
      |
      ↓
Security Group correct?
      |
      ↓
DNS working?
      |
      ↓
OS/service working?
      |
      ↓
Test again
```

---

## Senior-Level Understanding

Senior engineers don't troubleshoot by guessing.

They start with:

```text
Source
Destination
Protocol
Port
Expected path
```

For example:

```text
Source:
10.0.2.10

Destination:
Internet

Protocol:
HTTPS

Port:
443
```

Then trace:

```text
10.0.2.10
    ↓
Private Route Table
    ↓
NAT Gateway
    ↓
Public Route Table
    ↓
Internet Gateway
    ↓
Internet
```

### Example diagnosis

If:

```bash
ping 8.8.8.8
```

works but:

```bash
nslookup google.com
```

fails,

investigate DNS.

If DNS works but:

```bash
curl -I https://google.com
```

fails, investigate:

- Routing
- Security
- Proxy
- Firewall
- TLS
- Application-level issues

The principle is:

> Don't change configuration until you understand where the failure is.

---

## Interview Answer

"When troubleshooting AWS network connectivity, I first identify the source, destination, protocol and expected traffic path. Then I validate the EC2 state and IP configuration, followed by subnet and route table configuration. I check whether the required gateway, such as an Internet Gateway or NAT Gateway, is correctly configured. After routing, I validate Security Groups and DNS, and finally check the operating system and application service. On Linux I use commands such as `ip addr`, `ip route`, `nslookup`, `dig`, `curl` and `systemctl`. My general approach is to follow the packet rather than randomly changing configurations."


---

# 10. Complete Day 02 Architecture

The concepts from Day 02 come together like this:

```text
                              INTERNET
                                  |
                                  |
                           INTERNET GATEWAY
                                  |
                    +-------------+-------------+
                    |                           |
                    ↓                           ↓
              PUBLIC SUBNET              PRIVATE SUBNET
                    |                           |
              PUBLIC ROUTE                PRIVATE ROUTE
                    |                           |
              0.0.0.0/0 → IGW          0.0.0.0/0 → NAT
                    |                           |
                    ↓                           ↓
              BASTION HOST                 PRIVATE EC2
                                                |
                                                ↓
                                           APPLICATION
```

---

# Private EC2 → Internet

```text
Private EC2
     |
     ↓
Private Subnet
     |
     ↓
Private Route Table
     |
     ↓
NAT Gateway
     |
     ↓
Public Subnet
     |
     ↓
Public Route Table
     |
     ↓
Internet Gateway
     |
     ↓
Internet
```

---

# Administrator → Private EC2

```text
Administrator Laptop
        |
        ↓
     Internet
        |
        ↓
   Internet Gateway
        |
        ↓
  Public Route Table
        |
        ↓
   Public Subnet
        |
        ↓
   Bastion Host
        |
        ↓
  Private Subnet
        |
        ↓
    Private EC2
```

---

# Security Flow

```text
Traffic
   |
   ↓
Route Table
   |
   ↓
Network Path
   |
   ↓
Security Group
   |
   ↓
Allowed?
   |
   +---- YES → Application
   |
   └---- NO → Blocked
```

Remember:

```text
Route Table
→ Where should traffic go?

Security Group
→ Is traffic allowed?

NAT Gateway
→ How does private infrastructure reach the internet?

Bastion
→ How can an administrator reach private infrastructure?
```

---

# Day 02 — Practical Commands

## Linux Networking

```bash
ip addr
```

```bash
ip route
```

```bash
ping <ip>
```

```bash
nslookup google.com
```

```bash
dig google.com
```

```bash
curl -I https://google.com
```

---

## SSH

```bash
chmod 400 key.pem
```

```bash
ssh -i key.pem ubuntu@<public-ip>
```

---

## Service Troubleshooting

```bash
sudo systemctl status ssh
```

---

## Package Connectivity

```bash
sudo apt update
```

---

# Day 02 — Senior Mental Model

When an EC2 cannot communicate, think in this order:

```text
1. Is the instance running?
          ↓
2. Does it have the expected IP?
          ↓
3. Which subnet is it in?
          ↓
4. Which route table is associated?
          ↓
5. Where does the default route point?
          ↓
6. Is NAT/IGW required?
          ↓
7. Is the Security Group allowing traffic?
          ↓
8. Is DNS working?
          ↓
9. Is the Linux OS routing correctly?
          ↓
10. Is the application/service running?
```

---

# One-Line Memory Hooks

```text
Public Subnet
→ Has a route to Internet Gateway

Private Subnet
→ No direct Internet Gateway route

NAT Gateway
→ Private resources can initiate outbound internet connections

Elastic IP
→ Persistent public IPv4

Security Group
→ Stateful traffic access control

Bastion Host
→ Controlled administrative entry point

Private EC2
→ Internal workload without direct public exposure

SSH
→ Secure remote administration

DNS
→ Name resolution and infrastructure abstraction

Network Troubleshooting
→ Identify source → destination → path → failure point
```

---

# Day 02 Final Understanding

The most important lesson from Day 02 is understanding the **complete traffic path**.

For example, when a private EC2 runs:

```bash
sudo apt update
```

don't simply think:

> "Internet is not working."

Think:

```text
Private EC2
     ↓
Private Subnet
     ↓
Private Route Table
     ↓
NAT Gateway
     ↓
Public Subnet
     ↓
Public Route Table
     ↓
Internet Gateway
     ↓
Internet
     ↓
Package Repository
```

If it fails, identify which part of this path is broken.

That is the difference between:

> "I know AWS services."

and:

> "I understand how AWS networking actually works."
```
