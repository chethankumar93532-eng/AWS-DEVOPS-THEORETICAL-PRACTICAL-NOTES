Day 1 
 
Topics covered 
AWS VPC Fundamentals 
CIDR & Subnets 
Availability Zones 
Internet Gateway 
Route Tables 
 
Hands-on Practice 
Created a VPC: day01-Test 
CIDR: 10.0.0.0/16 
Created 3 public subnets across us-east-1a, us-east-1b, and us-east-1c 
Configured Route Table and Internet Gateway 
Launched Ubuntu EC2 instance (t3.micro) 
Connected to the EC2 instance using EC2 Instance Connect 
Installed/configured Nginx 
Successfully accessed the Nginx welcome page using the EC2 public IP 
And change HTML file to my learning Devops journey page 
 
FYR, I attached screenshot if I missed anything pls let me know 
 
Troubleshooting 
I was facing connecting issue while try to connect ec2 instance it showing connection failed. Issue is forgotten to add Route table to Internet gateway then i checked manually & added then I can be able to connect ec2 instance. 
 
Key Learning 
Understood the basic relationship between VPC → Subnet → Route Table → Internet Gateway → EC2 and how a web server can be deployed on an EC2 instance. 
Status Day1 Completed. 
 
Screenshot FYR 
 

 
 
 
 
 
 
 
 

 
 
 


