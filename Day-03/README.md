# Day-03 — AWS Storage & Networking

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/420ada6f-514b-497c-98e3-82c73d783d12" />



## Part 1 — AWS EBS & Linux Disk Partitioning

### Hands-On
[ EBS Hands-On & Evidence ](AWS-EBS-PARTITION.md)

### Study Notes
[ EBS Study & Interview Preparation ](Part-01-EBS-Study/AWS-EBS-PARTITION-Study.md)

### Architecture
AWS EBS → EC2 → Linux Disk → Partition → Filesystem → Mount → /etc/fstab → Persistent Storage → Snapshot

## Part 2 — VPC Endpoints

[ VPC Endpoint Hands-On ](AWS-EC2-ENDPOINTS.md)

## Evidence

All hands-on screenshots are available in the `screenshots/` directory.

-----------------------------------------------------------------------------------------------------------------

## Part 2 — VPC Endpoint & S3 Private Connectivity

![AWS VPC Endpoint & S3 3D Architecture](screenshots/18-vpc-endpoint-s3-3d-architecture.png)

### Architecture Flow

Private EC2
↓
Route Table
↓
S3 Gateway VPC Endpoint
↓
Amazon S3

The architecture demonstrates private connectivity from an EC2 instance inside a VPC to Amazon S3 using an S3 Gateway VPC Endpoint, without requiring a NAT Gateway or public internet path.
