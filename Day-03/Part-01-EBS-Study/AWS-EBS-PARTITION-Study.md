Day-03 — Part 1: AWS EBS & Linux Disk Partitioning
1. WHY — Problem Solved
Imagine you have an EC2 server running an application.
The EC2 instance needs storage for:
* Operating system
* Application files
* Logs
* Database data
* User/application data
* Backups
The root EBS volume contains the operating system, but in a production environment, you often don't want all application data sitting on the root disk.
So we create an additional EBS volume, attach it to EC2, and configure Linux to use it.
The important point is:
Creating an EBS volume in AWS does not automatically make it usable as a Linux filesystem.
You need to:
EBS Volume
    ↓
Attach to EC2
    ↓
Linux detects disk
    ↓
Partition
    ↓
Create filesystem
    ↓
Get UUID
    ↓
Mount
    ↓
Configure /etc/fstab
    ↓
Persistent storage
Business value
This gives us:
* Separate application storage
* Better storage management
* Independent backup/snapshot strategy
* Easier expansion
* Better operational control
* Reduced dependency on the root filesystem

2. HOW — Architecture
Think of the architecture like this:
                         AWS
                          │
                        Region
                          │
                          ▼
                         VPC
                          │
                       Subnet
                          │
                          ▼
                     ┌─────────┐
                     │   EC2   │
                     └────┬────┘
                          │
             ┌────────────┴────────────┐
             │                         │
             ▼                         ▼
        Root EBS                 Additional EBS
             │                         │
             ▼                         ▼
            OS                    Linux Disk
                                       │
                                       ▼
                                   Partition
                                       │
                                       ▼
                                   Filesystem
                                       │
                                       ▼
                                  Mount Point
                                      /data
                                       │
                                       ▼
                                Application Data
Main components
EC2
Compute instance where Linux is running.
EBS Volume
Persistent block storage attached to EC2.
Partition
Logical division of a disk.
Example:
/dev/nvme1n1
        ↓
/dev/nvme1n1p1
Filesystem
Makes the partition usable for storing files.
Example:
ext4
xfs
UUID
Unique identifier of the filesystem.
Example:
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Mount Point
Directory where the filesystem becomes accessible.
Example:
/data
EBS Snapshot
Point-in-time backup of an EBS volume.

3. END-TO-END FLOW
Here's the complete lifecycle you should remember:
1. Create EBS volume
        ↓
2. Attach EBS to EC2
        ↓
3. Linux detects new disk
        ↓
4. Identify disk
        ↓
5. Create partition
        ↓
6. Create filesystem
        ↓
7. Get UUID
        ↓
8. Create mount directory
        ↓
9. Mount filesystem
        ↓
10. Verify storage
        ↓
11. Configure /etc/fstab
        ↓
12. Test persistent mount
        ↓
13. Reboot and verify
        ↓
14. Snapshot / backup
Very important distinction
AWS knows:
"This EBS volume is attached to this EC2."
Linux still needs:
"How should I partition, format and mount this disk?"
That's why the AWS layer and Linux layer both matter.

4. LEARN & IMPLEMENT — HANDS-ON
Step 1 — Verify EC2
Check the instance:
aws ec2 describe-instances
Check the instance from Linux:
hostname

Step 2 — Check existing disks
Run:
lsblk
And:
df -h
You should identify the existing root filesystem.
Example:
NAME        SIZE TYPE MOUNTPOINT
nvme0n1      20G disk
└─nvme0n1p1  20G part /
At this point:
nvme0n1 → existing disk
/       → root filesystem

Step 3 — Inspect disks
Run:
sudo fdisk -l
This gives detailed disk information.
You're looking for the newly attached EBS device.

Step 4 — Attach the EBS volume
From AWS Console:
EC2
 ↓
Elastic Block Store
 ↓
Volumes
 ↓
Select EBS volume
 ↓
Actions
 ↓
Attach volume
 ↓
Select EC2 instance
 ↓
Attach
Then return to Linux.
Run:
lsblk
You should now see an additional disk.
For example:
nvme0n1       20G
└─nvme0n1p1   20G /
nvme1n1       10G
The important observation:
nvme1n1
exists, but it doesn't necessarily have a filesystem or mount point yet.

Step 5 — Create Partition
Use:
sudo fdisk /dev/nvme1n1
Inside fdisk:
n
Create a new partition.
Then:
p
to print the partition table.
Then:
w
to write the changes.
Verify:
lsblk
Now you should see:
nvme1n1
└─nvme1n1p1

Step 6 — Create Filesystem
Create an ext4 filesystem:
sudo mkfs.ext4 /dev/nvme1n1p1
Then check the filesystem:
sudo blkid
You'll see something similar to:
/dev/nvme1n1p1: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="ext4"
The UUID is important.

Step 7 — Create Mount Point
Create:
sudo mkdir /data
Now /data will become the location where your EBS filesystem is mounted.

Step 8 — Mount the Filesystem
Run:
sudo mount /dev/nvme1n1p1 /data
Verify:
df -h
And:
lsblk
You should see /data.

Step 9 — Test Data
Create a file:
sudo touch /data/testfile
Check:
ls -l /data
If you see:
testfile
your filesystem is working.

5. PERSISTENT MOUNT
Now comes an important production concept.
If you manually run:
mount /dev/nvme1n1p1 /data
the mount may not automatically survive a reboot.
We configure /etc/fstab.
First get the UUID:
sudo blkid /dev/nvme1n1p1
Then edit:
sudo vi /etc/fstab
Add:
UUID=<YOUR-UUID> /data ext4 defaults,nofail 0 2
Why UUID?
Don't depend on:
/dev/nvme1n1p1
because device naming can change.
Instead use:
UUID=<filesystem-UUID>
This identifies the filesystem itself.
Test before reboot
This is extremely important.
Run:
sudo mount -a
Then:
df -h
If there is an /etc/fstab mistake, you want to discover it before rebooting.

6. HANDS-ON PROOF GATE — BREAK & FIX
Now don't just build it.
Break it deliberately.
That's how you move from junior to senior.
Failure 1 — Filesystem is not mounted
Check:
df -h
Then:
lsblk
If the disk exists but isn't mounted:
sudo mount /dev/nvme1n1p1 /data
Verify:
df -h
Lesson
Disk exists ≠ filesystem mounted.

Failure 2 — Incorrect /etc/fstab
Check:
cat /etc/fstab
Get the actual UUID:
sudo blkid
Compare them.
If the UUID is incorrect, fix /etc/fstab.
Then:
sudo mount -a
And:
df -h
Lesson
Never blindly reboot after changing /etc/fstab.
Always test:
sudo mount -a

Failure 3 — Disk usage problem
Check:
df -h
Create a test file:
sudo fallocate -l 500M /data/test-large-file
Check:
df -h
Remove it:
sudo rm /data/test-large-file
Check again:
df -h
Lesson
You should understand both:
Filesystem capacity
        ↓
df -h
and:
Directory/file usage
        ↓
du

7. WHAT-IF — FAILURE & TROUBLESHOOTING
What if EBS is attached but Linux doesn't see it?
Check:
lsblk
Then:
dmesg | tail
Also verify the AWS console attachment.

What if partition exists but filesystem doesn't?
Check:
lsblk
and:
sudo blkid
If there is no filesystem, create one:
sudo mkfs.ext4 /dev/nvme1n1p1

What if filesystem exists but isn't mounted?
Check:
df -h
Then:
sudo mount /dev/nvme1n1p1 /data

What if mount disappears after reboot?
Check:
cat /etc/fstab
Then:
sudo blkid
Verify:
UUID
Filesystem type
Mount point
Mount options

What if disk becomes full?
Check:
df -h
Then investigate:
sudo du -sh /data/*
Now you can determine which directories/files are consuming storage.

8. SENIOR DECISION LAYER
This is where interviewers start testing whether you understand production.
EBS vs S3
EBS
Block storage.
Used primarily with EC2 workloads that require filesystem/block-device semantics.
EC2
 ↓
EBS
 ↓
Filesystem
 ↓
Application
S3
Object storage.
Application
 ↓
S3 API
 ↓
Object
You don't normally mount S3 like an ordinary EBS block device.

EBS Snapshot
An EBS snapshot provides a point-in-time backup mechanism for an EBS volume.
Production considerations include:
* Backup strategy
* Encryption
* Retention
* Recovery
* Lifecycle management
* Cost

Performance
Don't simply ask:
"How much storage do I need?"
You should also consider:
* IOPS
* Throughput
* Latency
* Workload pattern
* Read/write behavior
For example:
Database workload
        ↓
High I/O requirements
        ↓
Storage performance matters

Security
Production storage should consider:
* EBS encryption
* IAM permissions
* Least privilege
* Access controls
* Snapshot security

Reliability
Think about:
EBS
 ↓
Snapshot
 ↓
Backup
 ↓
Recovery
And monitor:
* Disk utilization
* IOPS
* Throughput
* Latency
* Filesystem health

9. INTERVIEW ANSWER
You can answer like this:
"In my hands-on AWS work, I used EBS as persistent block storage for an EC2 instance. I created an additional EBS volume and attached it to the instance, then verified from Linux using lsblk and fdisk that the operating system detected the new disk. I created a partition on the disk, formatted it with ext4, retrieved the filesystem UUID using blkid, created a /data mount point, and mounted the filesystem there."
"I then configured /etc/fstab using the filesystem UUID so that the mount would persist across reboots. Before rebooting, I tested the configuration using mount -a because an incorrect fstab entry can cause boot or mount problems."
"I also practiced troubleshooting scenarios such as a disk being attached but not mounted, an incorrect UUID in fstab, and filesystem capacity issues. I used commands such as lsblk, df -h, blkid, fdisk, mount, dmesg, and du to identify and troubleshoot the problems."
"From a production perspective, I would also consider EBS encryption, snapshots and backup retention, storage performance such as IOPS and throughput, monitoring, and recovery requirements. I would choose EBS when the workload needs block storage associated with EC2, while object-based data would generally be handled using S3."

10. COMMANDS USED / PRACTICE COMMANDS
AWS
aws ec2 describe-instances
Disk discovery
lsblk
df -h
sudo fdisk -l
Partition
sudo fdisk /dev/nvme1n1
Filesystem
sudo mkfs.ext4 /dev/nvme1n1p1
sudo blkid
Mount
sudo mkdir /data
sudo mount /dev/nvme1n1p1 /data
Verification
df -h
lsblk
ls -l /data
Persistent mount
sudo vi /etc/fstab
sudo mount -a
Troubleshooting
cat /etc/fstab
dmesg | tail
sudo du -sh /data/*
Break/Fix test
sudo touch /data/testfile
sudo fallocate -l 500M /data/test-large-file
sudo rm /data/test-large-file

🧠 DAY-03 PART-1 MEMORY HOOK
EBS
 ↓
Attach
 ↓
Detect
 ↓
Partition
 ↓
Filesystem
 ↓
UUID
 ↓
Mount
 ↓
/etc/fstab
 ↓
Persistent Storage
 ↓
Snapshot / Backup
One-line memory
EBS gives EC2 persistent block storage; Linux then needs to detect, partition, format, mount, and persist that storage before the application can use it.
Part 1 complete. When you say “Part 1 done”, we'll move to Day-03 Part 2 — VPC Endpoints.
