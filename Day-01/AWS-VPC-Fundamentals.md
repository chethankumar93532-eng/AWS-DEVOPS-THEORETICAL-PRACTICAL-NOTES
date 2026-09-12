# Day 01 — AWS VPC Fundamentals

Copy everything below and save it as:

`Day-01/AWS-VPC-Fundamentals.md`

```markdown
# Day 01 — AWS VPC Fundamentals

## Topics Covered

- AWS VPC
- CIDR
- Subnets
- Availability Zones
- Route Tables
- Internet Gateway
- Public and Private Network Concepts
- Basic AWS Networking

---

# 1. AWS VPC

## Scenario

Imagine a company wants to deploy an application in AWS.

The application contains:

- Web servers
- Application servers
- Database servers

The company does not want every server to be directly accessible from the internet.

It needs to control:

- Which IP addresses are used
- Which resources can communicate
- Which resources are public
- Which resources remain private
- How traffic moves
- How internet connectivity is provided

AWS VPC provides the foundation for this network.

---

## Core Concept

A VPC (Virtual Private Cloud) is a logically isolated network that we create inside AWS to control how our cloud resources communicate.

### Simple way to remember

> VPC = Our own logically isolated network inside AWS.

---

## Why

A VPC is required because AWS resources need a controlled networking environment.

It allows us to design:

- IP address ranges
- Subnets
- Routing
- Internet connectivity
- Network isolation
- Security boundaries

Without a properly designed VPC, it becomes difficult to control how resources communicate with each other and with external networks.

### Business perspective

A company needs to answer questions such as:

> Should this server be accessible from the internet?

> Should this database be accessible directly from the internet?

> Which application servers can communicate with the database?

> How should private servers access external services?

VPC networking provides the foundation for answering these questions.

---

## Components

Important VPC-related components include:

- VPC
- CIDR block
- Subnets
- Availability Zones
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Network ACLs
- DNS
- EC2 instances

The components work together rather than operating independently.

---

## End-to-End Flow

A simplified AWS network looks like:

```text
AWS Region
    |
    ↓
VPC
    |
    +-------------------+
    |                   |
    ↓                   ↓
Public Subnet       Private Subnet
    |                   |
    ↓                   ↓
Web/Bastion          Application
                        |
                        ↓
                     Database
```

The VPC provides the overall network boundary.

The subnets divide the VPC into smaller networks.

Route tables determine where traffic should go.

Gateways provide connectivity to other networks.

Security controls determine which traffic is allowed.

---

## Practical

Example VPC:

```text
VPC CIDR:
10.0.0.0/16
```

Create subnets:

```text
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

Basic architecture:

```text
VPC
 |
 +---- Public Subnet
 |
 +---- Private Subnet
```

---

## Senior-Level Understanding

A senior engineer does not think about VPC as just:

> "Create a VPC and select a CIDR."

The VPC is an important architectural boundary.

Before selecting the CIDR, consider:

- Number of subnets
- Number of Availability Zones
- Expected workload growth
- Multiple environments
- VPC peering
- Transit Gateway
- VPN
- Direct Connect
- On-premises connectivity
- Future IP requirements
- Overlapping CIDR ranges

For example, if today's requirement is only:

```text
10.0.0.0/24
```

but the organization may later have many workloads, choosing a larger and well-planned address space may be better.

However, unnecessarily huge or poorly planned address spaces can also create operational problems.

The important principle is:

> Design the network for future connectivity, not only today's workload.

---

## Interview Answer

"A VPC is the logically isolated network boundary I use for AWS workloads. It allows me to define the IP address space, create subnets, control routing and design how resources communicate with each other and external networks. For example, I would typically separate internet-facing components from internal application and database workloads using public and private subnets. From a senior perspective, VPC design is not just about creating the network; I also consider CIDR planning, future growth, multiple Availability Zones, connectivity to other VPCs or on-premises networks, and avoiding overlapping IP ranges."

---

# 2. CIDR

## Scenario

After creating a VPC, AWS needs to know:

> Which IP addresses belong to this network?

For example:

```text
10.0.0.0/16
```

This tells AWS the IP address space available for the VPC.

---

## Core Concept

CIDR (Classless Inter-Domain Routing) is a notation used to define an IP address range and its network size.

### Simple way to remember

> CIDR = Defines the network's IP address space.

---

## Why

CIDR is important because every network needs an IP address range.

It helps us:

- Define network size
- Allocate IP addresses
- Create subnets
- Plan network growth
- Design routing
- Connect different networks

---

## Components

Example:

```text
10.0.0.0/16
```

Here:

```text
10.0.0.0
```

is the network address.

```text
/16
```

represents the prefix length.

A `/16` network provides a much larger address space than a `/24`.

For example:

```text
10.0.0.0/16
```

can be divided into smaller networks:

```text
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
```

---

## End-to-End Flow

Think of CIDR as the parent network:

```text
VPC
10.0.0.0/16
      |
      +---- Public Subnet
      |     10.0.1.0/24
      |
      +---- Private App
      |     10.0.2.0/24
      |
      +---- Private DB
            10.0.3.0/24
```

The VPC owns the larger address space.

Subnets consume smaller portions of that address space.

---

## Practical

Example design:

```text
VPC:
10.0.0.0/16

Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

Check the network configuration using AWS Console or AWS CLI.

Example CLI:

```bash
aws ec2 describe-vpcs
```

---

## Senior-Level Understanding

CIDR planning becomes extremely important when environments grow.

Imagine:

```text
VPC A:
10.0.0.0/16

VPC B:
10.0.0.0/16
```

Both VPCs can exist independently.

But later the company wants:

```text
VPC A <--------> VPC B
```

through VPC Peering or Transit Gateway.

Now the overlapping CIDR ranges create a problem because AWS cannot cleanly route traffic between overlapping address spaces.

This is why senior engineers think about:

> Future network connectivity before selecting CIDRs.

CIDR planning is especially important for:

- Multi-VPC architectures
- Hybrid cloud
- VPN
- Transit Gateway
- Kubernetes networking
- On-premises connectivity

---

## Interview Answer

"CIDR is the notation used to define an IP network and its size. In AWS, I first assign a CIDR block to the VPC and then divide that address space into smaller subnet ranges. CIDR planning is important because it affects the number and size of subnets and also future connectivity. For example, if multiple VPCs or on-premises networks may need to communicate later, I avoid overlapping CIDR ranges because overlapping addresses can create routing problems."

---

# 3. Subnets

## Scenario

Suppose our VPC contains:

```text
Web Server
Application Server
Database
```

We don't want to place every workload in the same network segment.

We need network separation.

So we divide the VPC into smaller networks called subnets.

---

## Core Concept

A subnet is a smaller IP network created inside a VPC to organize and isolate resources.

### Simple way to remember

> VPC = Large network

> Subnet = Smaller network inside the VPC

---

## Why

Subnets provide:

- Network segmentation
- Different routing behavior
- Workload separation
- Security boundaries
- Availability Zone placement

For example:

```text
Public Subnet
→ Internet-facing resources

Private Subnet
→ Internal application resources
```

---

## Components

A typical architecture can be:

```text
VPC
 |
 +---- Public Subnet
 |        |
 |        +---- Load Balancer
 |        +---- Bastion
 |
 +---- Private Subnet
          |
          +---- Application Server
          +---- Database
```

---

## End-to-End Flow

Public workload:

```text
Internet
   |
Internet Gateway
   |
Public Route Table
   |
Public Subnet
   |
EC2
```

Private workload:

```text
Application
   |
Private Subnet
   |
Private Route Table
   |
Internal resources
```

If private resources need outbound internet:

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

## Practical

Example:

```text
VPC:
10.0.0.0/16

Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

When creating a subnet, you select:

- VPC
- Availability Zone
- CIDR block

Example:

```text
Subnet:
10.0.1.0/24

Availability Zone:
AZ-A
```

---

## Senior-Level Understanding

A common beginner explanation is:

> Public subnet means a subnet with a public IP.

That is not the correct architectural definition.

The better definition is:

> A subnet is considered public when its associated route table has a route to an Internet Gateway.

A private subnet does not have a direct route to an Internet Gateway for internet access.

Also:

> Private does not necessarily mean "no internet."

A private subnet can have outbound internet access through a NAT Gateway.

---

## Interview Answer

"I use subnets to divide a VPC into smaller network segments based on workload and routing requirements. For example, internet-facing components can be placed in public subnets, while application and database workloads are normally placed in private subnets. The important distinction is that public or private is primarily determined by the routing configuration, not simply by whether an EC2 has a public IP. In production, I would also distribute important subnets across multiple Availability Zones for resilience."

---

# 4. Availability Zones

## Scenario

Suppose our entire application runs in one infrastructure location.

If that location experiences a failure, our application could become unavailable.

To reduce this risk, we distribute workloads across multiple Availability Zones.

---

## Core Concept

An Availability Zone is an isolated infrastructure location within an AWS Region designed to provide failure isolation.

### Simple way to remember

> Region = Geographic area

> Availability Zone = Isolated infrastructure location inside the Region

---

## Why

Multiple Availability Zones help improve:

- High availability
- Fault tolerance
- Resilience
- Failure isolation

If one AZ has an infrastructure problem, workloads in another AZ can potentially continue operating.

---

## Components

Example:

```text
AWS Region
    |
    +---- Availability Zone A
    |         |
    |         +---- Public Subnet
    |         +---- Private Subnet
    |
    +---- Availability Zone B
              |
              +---- Public Subnet
              +---- Private Subnet
```

---

## End-to-End Flow

A highly available application could look like:

```text
                Load Balancer
                 /        \
                /          \
             AZ-A          AZ-B
              |              |
           EC2/App        EC2/App
```

Traffic can be distributed across application instances in different AZs.

---

## Practical

Example architecture:

```text
Region
 |
 +---- AZ-A
 |      |
 |      +---- Public Subnet
 |      +---- Private App Subnet
 |
 +---- AZ-B
        |
        +---- Public Subnet
        +---- Private App Subnet
```

---

## Senior-Level Understanding

Simply deploying resources into two AZs does not automatically guarantee high availability.

You need to consider the entire system.

For example:

```text
Application → Multi-AZ
Database    → Single-AZ
```

The database could still become the single point of failure.

A senior engineer considers:

- Application layer
- Database layer
- Load balancer
- Storage
- Networking
- Deployment process
- Dependencies
- Monitoring
- Recovery strategy

The real objective is:

> Remove or reduce single points of failure.

---

## Interview Answer

"Availability Zones are isolated infrastructure locations within an AWS Region. I use multiple AZs to reduce the impact of failures affecting a single location. For example, application instances can be distributed across multiple AZs behind a load balancer. However, multi-AZ deployment by itself doesn't guarantee high availability. I need to evaluate the entire architecture, including the database, storage, networking, dependencies and deployment strategy, to make sure the system can tolerate failures."

---

# 5. Route Tables

## Scenario

Imagine an EC2 instance wants to communicate with the internet.

AWS needs to answer:

> Where should this traffic go?

The route table provides this decision.

---

## Core Concept

A route table contains rules that determine where network traffic should be sent.

### Simple way to remember

> Route Table = Where should the packet go?

---

## Why

A resource can have an IP address but still have no connectivity if there is no correct route.

Routing determines the path between:

- Subnets
- VPC resources
- Gateways
- Other networks
- Internet

---

## Components

Example public route table:

```text
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Meaning:

```text
10.0.0.0/16
→ Keep traffic inside the VPC

0.0.0.0/0
→ Send traffic for other destinations to the Internet Gateway
```

---

## End-to-End Flow

Public EC2:

```text
EC2
 |
Subnet
 |
Route Table
 |
0.0.0.0/0
 |
Internet Gateway
 |
Internet
```

Private EC2 with NAT:

```text
Private EC2
 |
Private Route Table
 |
0.0.0.0/0
 |
NAT Gateway
 |
Public Route Table
 |
Internet Gateway
 |
Internet
```

---

## Practical

Create a public route:

```text
Destination:
0.0.0.0/0

Target:
Internet Gateway
```

Associate that route table with the public subnet.

For a private subnet using NAT:

```text
Destination:
0.0.0.0/0

Target:
NAT Gateway
```

---

## Senior-Level Understanding

Routing and security are two different concepts.

Remember:

```text
Route Table
     ↓
Where should traffic go?

Security Group
     ↓
Is the traffic allowed?
```

Having a route does not mean traffic will automatically be permitted.

For example:

```text
EC2
 |
Route exists
 |
Security Group blocks port
 |
Connection fails
```

This distinction is extremely important during troubleshooting.

---

## Interview Answer

"A route table defines where traffic from a subnet should be sent. For example, a public subnet normally has a default route to an Internet Gateway, while a private subnet that requires outbound internet access can have a default route to a NAT Gateway. When troubleshooting connectivity, I separate routing from security because the route table determines the network path, while Security Groups and other controls determine whether the traffic is allowed."

---

# 6. Internet Gateway

## Scenario

Our public EC2 needs to communicate with the internet.

The VPC needs a component that provides the connection between the VPC and the internet.

That component is the Internet Gateway.

---

## Core Concept

An Internet Gateway is a VPC component that provides the network path between a VPC and the internet.

### Simple way to remember

> Internet Gateway = VPC's internet connectivity path.

---

## Why

Without an Internet Gateway, a VPC cannot use the normal internet gateway-based path for internet communication.

However, simply attaching an Internet Gateway is not enough.

Other components must also be configured correctly.

---

## Components

For a public EC2:

```text
Internet
   |
Internet Gateway
   |
Route Table
   |
Public Subnet
   |
EC2
```

The EC2 also needs appropriate public addressing and security configuration.

---

## End-to-End Flow

Incoming request:

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
EC2
```

The return traffic follows the appropriate network and security state.

---

## Practical

Public route table:

```text
Destination:
0.0.0.0/0

Target:
Internet Gateway
```

Example architecture:

```text
VPC
 |
Internet Gateway
 |
Public Route Table
 |
Public Subnet
 |
EC2
```

---

## Senior-Level Understanding

A very common mistake is thinking:

> "I attached an Internet Gateway, so my EC2 is now public."

That is incorrect.

For an EC2 to have normal internet-facing connectivity, multiple things need to align:

```text
VPC
 |
Internet Gateway
 |
Route Table
 |
Public Subnet
 |
Public IP
 |
Security Group
 |
EC2
```

If one important part is missing or incorrectly configured, connectivity can fail.

---

## Interview Answer

"An Internet Gateway provides the network path between a VPC and the internet. However, attaching an IGW to a VPC doesn't automatically make resources public. The subnet needs an appropriate route to the IGW, the resource needs suitable public addressing, and the security controls must permit the required traffic. When troubleshooting, I validate the complete path from the source to the destination instead of checking only whether the IGW exists."

---

# Day 01 — Complete Architecture

The main concepts from Day 01 can be combined into this architecture:

```text
                         AWS REGION
                             |
                             ↓
                           VPC
                       10.0.0.0/16
                             |
              +--------------+--------------+
              |                             |
              ↓                             ↓
        PUBLIC SUBNET                 PRIVATE SUBNET
        10.0.1.0/24                  10.0.2.0/24
              |                             |
              ↓                             ↓
        ROUTE TABLE                  ROUTE TABLE
              |                             |
              ↓                             ↓
        INTERNET GATEWAY              Internal
              |                       Resources
              ↓
           INTERNET
```

---

# Day 01 — What I Should Be Able To Explain

After Day 01, I should be able to explain:

### 1. Why do we need a VPC?

To create a logically isolated and controlled network environment for AWS resources.

### 2. Why do we need CIDR?

To define the IP address space of the VPC and plan subnet allocation.

### 3. Why do we need subnets?

To divide the VPC into smaller network segments with different routing and workload purposes.

### 4. What is an Availability Zone?

An isolated infrastructure location within an AWS Region used to improve resilience and failure isolation.

### 5. What does a Route Table do?

It determines where network traffic should be sent.

### 6. What does an Internet Gateway do?

It provides the VPC's path to and from the internet for appropriately configured public resources.

### 7. What makes a subnet public?

Its routing configuration provides a path to an Internet Gateway.

### 8. What makes a subnet private?

It does not have a direct internet route through an Internet Gateway.

---

# Day 01 — Senior Mental Model

Always think about AWS networking in this order:

```text
1. VPC
   ↓
2. CIDR
   ↓
3. Subnets
   ↓
4. Availability Zones
   ↓
5. Route Tables
   ↓
6. Gateways
   ↓
7. Security
   ↓
8. Workloads
```

And whenever connectivity fails, ask:

```text
Who is communicating?
        ↓
What is the source IP?
        ↓
What is the destination?
        ↓
Which subnet?
        ↓
Which route table?
        ↓
Which gateway?
        ↓
Is the traffic allowed?
        ↓
Is the destination reachable?
```

---

# One-Line Memory Hooks

```text
VPC
→ Network boundary

CIDR
→ IP address space

Subnet
→ Network segmentation

Availability Zone
→ Failure isolation

Route Table
→ Where should traffic go?

Internet Gateway
→ VPC internet path

Public Subnet
→ Has a route to Internet Gateway

Private Subnet
→ No direct internet route through Internet Gateway
```

---

# Final Day 01 Understanding

The most important thing is not memorizing individual AWS services.

Understand how they connect:

```text
                    AWS
                     |
                    VPC
                     |
                   CIDR
                     |
                  Subnets
                /          \
          PUBLIC            PRIVATE
             |                 |
        Route Table       Route Table
             |                 |
            IGW              Internal
             |              Resources
             |
          Internet
```

The fundamental question behind Day 01 is:

> "How do I design a controlled network in AWS, divide it into meaningful sections, and determine how traffic moves between those sections and the internet?"

Once this mental model is clear, Day 02 becomes much easier because we start adding NAT Gateway, Security Groups, Bastion Host, Private EC2 and practical troubleshooting.
```
