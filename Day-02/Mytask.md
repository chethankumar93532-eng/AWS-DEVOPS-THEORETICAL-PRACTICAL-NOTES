Day 02 – AWS VPC & EC2 Networking (part 1)
 




Topics Covered
Public & Private Subnets
Internet Gateway (IGW)
NAT Gateway
Elastic IP
Route Tables
Security Groups
Bastion Host
Private EC2
SSH Connectivity
Basic Network Troubleshooting
Hands-on Work
Worked on the Sandpit VPC (10.0.0.0/16).
Configured Public and Private Subnets.
Deployed a Bastion Host in the Public Subnet.
Deployed a Private EC2 instance without a public IP.
Created a NAT Gateway in the Public Subnet and associated an Elastic IP.
Configured Public and Private Route Tables.
Practiced SSH access from Bastion Host → Private EC2.
Configured SSH key permissions using chmod 400.
Tested internet connectivity using ping.
Troubleshot Ubuntu apt update getting stuck at “Waiting for headers.”
Commands Used for Troubleshooting
ping google.com
ping www.youtube.com
sudo apt update
sudo apt update -o Acquire::ForceIPv4=true
curl -4 -I --max-time 10 http://ap-south-1.ec2.archive.ubuntu.com/ubuntu
curl -4 -I --max-time 10 http://security.ubuntu.com/ubuntu
ip route
cat /etc/apt/sources.list.d/ubuntu.sources
grep -n "URIs:" /etc/apt/sources.list.d/ubuntu.sources
Identified that the regional Ubuntu repository was reachable, while security.ubuntu.com was timing out, and updated the repository configuration accordingly.
Key Learning
Understood how a Bastion Host provides secure access to private EC2 instances and how a NAT Gateway + Elastic IP provides outbound internet access to private instances without assigning them public IPs.
Traffic Flow:
My Laptop
   ↓ SSH
Bastion Host
   ↓ SSH
Private EC2
   ↓
NAT Gateway → Internet Gateway → Internet
 
                    INTERNET
                        │
                        ▼
               Internet Gateway
                        │
              ┌─────────┴─────────┐
              │        VPC        │
              │    10.0.0.0/16    │
              │                   │
              │  PUBLIC SUBNET    │
              │       │           │
              │  Bastion Host     │
              │       │ SSH       │
              │       ▼           │
              │  PRIVATE SUBNET   │
              │       │           │
              │  Private EC2      │
              │       │           │
              │       ▼           │
              │  NAT Gateway      │
              │       │           │
              │   Elastic IP       │
              └───────┬───────────┘
                      │
                      ▼
                   INTERNET

VPC, EC2 Bastion host, NAT, Elastic IP Screenshots FYR
 









