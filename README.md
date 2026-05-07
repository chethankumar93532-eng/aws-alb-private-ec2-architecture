
<img width="1536" height="1024" alt="Aws high available Web application" src="https://github.com/user-attachments/assets/6035a75f-d61c-4460-918a-468e5c2725f7" />











# aws-alb-private-ec2-architecture

📌 Project Overview
This project demonstrates a production-style AWS architecture using:
* VPC
* Public & Private Subnets
* Internet Gateway
* Route Tables
* Bastion Host
* Application Load Balancer (ALB)
* Target Groups
* Security Groups
* Private EC2 Instances
* Python HTTP Server
* Health Checks
* High Availability
* Auto Scaling Concept
The architecture securely hosts applications inside private EC2 instances while exposing traffic through an Application Load Balancer.

⸻

🏗️ Architecture Diagram
Architecture Flow
Internet
   ↓
Application Load Balancer (ALB)
   ↓
Target Group
   ├── EC2 Instance 1 (Private)
   └── EC2 Instance 2 (Private)

⸻

🌐 Complete AWS End-to-End Architecture Flow

(VPC → Networking → Route Tables → ALB → EC2 → Browser)

┌────────────────────────────────────────────────────────────────────┐
│                            INTERNET                                │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
                               ▼
                     User Opens Browser
                               │
                               ▼
                   http://ALB-DNS-NAME
                               │
                               ▼
══════════════════════════════════════════════════════════════════════
                              AWS CLOUD
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                               VPC                                  │
│                         CIDR : 10.0.0.0/16                         │
│                                                                    │
│  Purpose: Logical isolated AWS network                             │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
            ┌──────────────────┴──────────────────┐
            │                                     │
            ▼                                     ▼
┌────────────────────────────┐      ┌────────────────────────────┐
│       PUBLIC SUBNET        │      │      PRIVATE SUBNET        │
│       10.0.1.0/24          │      │       10.0.2.0/24          │
│                            │      │                            │
│  Resources:                │      │  Resources:                │
│  • Application LB          │      │  • EC2 Instance 1          │
│  • Bastion Host            │      │  • EC2 Instance 2          │
│                            │      │  • ASG Instances           │
└──────────────┬─────────────┘      └──────────────┬─────────────┘
               │                                   │
               │                                   │
               ▼                                   ▼
══════════════════════════════════════════════════════════════════════
                         INTERNET GATEWAY
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                    INTERNET GATEWAY (IGW)                          │
│                                                                    │
│  Attached to VPC                                                   │
│  Allows Public Internet Access                                     │
└────────────────────────────────────────────────────────────────────┘
══════════════════════════════════════════════════════════════════════
                           ROUTE TABLES
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                    PUBLIC ROUTE TABLE                              │
├────────────────────────────────────────────────────────────────────┤
│ Destination          Target                                        │
│ 10.0.0.0/16          local                                         │
│ 0.0.0.0/0            Internet Gateway                              │
└────────────────────────────────────────────────────────────────────┘
Associated With:
• Public Subnet
┌────────────────────────────────────────────────────────────────────┐
│                   PRIVATE ROUTE TABLE                              │
├────────────────────────────────────────────────────────────────────┤
│ Destination          Target                                        │
│ 10.0.0.0/16          local                                         │
└────────────────────────────────────────────────────────────────────┘
Associated With:
• Private Subnet
══════════════════════════════════════════════════════════════════════
                         SECURITY GROUPS
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                  ALB SECURITY GROUP                                │
├────────────────────────────────────────────────────────────────────┤
│ Inbound Rules:                                                     │
│   HTTP 80 → 0.0.0.0/0                                              │
└────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────┐
│                 BASTION HOST SECURITY GROUP                        │
├────────────────────────────────────────────────────────────────────┤
│ Inbound Rules:                                                     │
│   SSH 22 → Your IP                                                 │
└────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────┐
│                 PRIVATE EC2 SECURITY GROUP                         │
├────────────────────────────────────────────────────────────────────┤
│ Inbound Rules:                                                     │
│   SSH 22 → Bastion Host SG                                         │
│   HTTP 80 → ALB Security Group                                     │
└────────────────────────────────────────────────────────────────────┘
══════════════════════════════════════════════════════════════════════
                     APPLICATION LOAD BALANCER
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                 APPLICATION LOAD BALANCER                          │
│                                                                    │
│  Type        : Internet Facing                                     │
│  Listener    : HTTP : 80                                           │
│  Subnets     : Public Subnets                                      │
│                                                                    │
│  Function:                                                          │
│  Receives browser requests and distributes traffic                  │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
                               ▼
══════════════════════════════════════════════════════════════════════
                           TARGET GROUP
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                         TARGET GROUP                               │
│                                                                    │
│ Protocol : HTTP                                                    │
│ Port     : 80                                                      │
│ Health Check Path : /                                              │
│                                                                    │
│ Registered Targets:                                                │
│   • EC2 Instance 1                                                 │
│   • EC2 Instance 2                                                 │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
               ┌───────────────┴────────────────┐
               │                                │
               ▼                                ▼
══════════════════════════════════════════════════════════════════════
                        PRIVATE EC2 INSTANCES
══════════════════════════════════════════════════════════════════════
┌────────────────────────────┐      ┌────────────────────────────┐
│      EC2 INSTANCE 1        │      │      EC2 INSTANCE 2        │
├────────────────────────────┤      ├────────────────────────────┤
│ Ubuntu                     │      │ Ubuntu                     │
│ Python HTTP Server         │      │ Python HTTP Server         │
│ Port 80                    │      │ Port 80                    │
│                            │      │                            │
│ HTML Response:             │      │ HTML Response:             │
│ "SERVER 1"                 │      │ "SERVER 2"                 │
└────────────────────────────┘      └────────────────────────────┘
══════════════════════════════════════════════════════════════════════
                       AUTO SCALING GROUP
══════════════════════════════════════════════════════════════════════
┌────────────────────────────────────────────────────────────────────┐
│                    AUTO SCALING GROUP                              │
│                                                                    │
│ Launch Template → Creates EC2 Automatically                        │
│                                                                    │
│ Scaling Policy Example:                                            │
│   CPU > 70% → Add New EC2                                          │
│   CPU < 30% → Remove EC2                                           │
│                                                                    │
│ Automatically Registers Instances to Target Group                  │
└────────────────────────────────────────────────────────────────────┘
══════════════════════════════════════════════════════════════════════
                            SSH FLOW
══════════════════════════════════════════════════════════════════════
MacBook
   │
   ▼
ssh -i aws-login.pem ubuntu@BASTION-PUBLIC-IP
   │
   ▼
Bastion Host
   │
   ▼
ssh -i aws-login.pem ubuntu@PRIVATE-IP
   │
   ▼
Private EC2
══════════════════════════════════════════════════════════════════════
                         COMMAND FLOW
══════════════════════════════════════════════════════════════════════
# Create HTML
echo "<h1>SERVER 1</h1>" > index.html
# Run Web Server
sudo python3 -m http.server 80
# ALB forwards traffic
Browser → ALB → Target Group → EC2
══════════════════════════════════════════════════════════════════════
                        BROWSER OUTPUT
══════════════════════════════════════════════════════════════════════
Refresh 1:
SERVER 1
Refresh 2:
SERVER 2
Refresh 3:
SERVER 1
(ALB Round Robin Traffic Distribution)
══════════════════════════════════════════════════════════════════════
                         FINAL RESULT
══════════════════════════════════════════════════════════════════════
✔ VPC Configured
✔ Public & Private Subnets Created
✔ Internet Gateway Attached
✔ Route Tables Configured
✔ Security Groups Configured
✔ Bastion Host Configured
✔ 2 Private EC2 Instances Running
✔ Python Web Servers Running
✔ Application Load Balancer Running
✔ Target Group Healthy
✔ Health Checks Passed
✔ Browser Accessible
✔ Traffic Distributed Between Both EC2s
✔ High Availability Achieved
✔ Auto Scaling Concept Added

⸻


ALB Security Group:
- HTTP 80 → 0.0.0.0/0

Bastion Host Security Group:
- SSH 22 → My IP

Private EC2 Security Group:
- SSH 22 → Bastion Host SG
- HTTP 80 → ALB Security Group

⸻

🧠 Project Objectives
* Build highly available architecture
* Learn ALB traffic distribution
* Configure private EC2 access securely
* Implement health checks
* Understand target groups
* Practice real-world DevOps troubleshooting

⸻

🛠️ AWS Services Used
Service	Purpose
VPC	Isolated AWS Network
Subnets	Public & Private Segmentation
Internet Gateway	Internet Access
Route Tables	Traffic Routing
Security Groups	Firewall Rules
EC2	Compute Instances
ALB	Traffic Distribution
Target Groups	Backend Registration
Bastion Host	Secure SSH Access
Auto Scaling Concept	Scalability
⸻

🔥 Step-by-Step Implementation
1️⃣ Create VPC
Created custom VPC:
CIDR: 10.0.0.0/16
Purpose:
* Isolated cloud network
* Custom networking control

⸻

2️⃣ Create Public & Private Subnets
Public Subnet
10.0.1.0/24
Resources:
* ALB
* Bastion Host
Private Subnet
10.0.2.0/24
Resources:
* EC2 Instance 1
* EC2 Instance 2

⸻

3️⃣ Attach Internet Gateway
Configured Internet Gateway and attached to VPC.
Purpose:
* Enable internet access for public subnet.

⸻

4️⃣ Configure Route Tables
Public Route Table
0.0.0.0/0 → Internet Gateway
Associated with Public Subnet.
Private Route Table
10.0.0.0/16 → local
Associated with Private Subnet.

⸻

5️⃣ Configure Security Groups
ALB Security Group
Type	Port	Source
HTTP	80	0.0.0.0/0
⸻

Bastion Host Security Group
Type	Port	Source
SSH	22	My IP
⸻

Private EC2 Security Group
Type	Port	Source
SSH	22	Bastion Host SG
HTTP	80	ALB SG
⸻

6️⃣ Launch EC2 Instances
Created:
* Bastion Host (Public EC2)
* Private EC2 Instance 1
* Private EC2 Instance 2
Operating System:
Ubuntu 24.04

⸻

7️⃣ SSH Access Configuration
SSH Into Bastion Host
ssh -i aws-login.pem ubuntu@PUBLIC-IP
Copy PEM File
scp -i aws-login.pem aws-login.pem ubuntu@PUBLIC-IP:~
SSH Into Private EC2
ssh -i aws-login.pem ubuntu@PRIVATE-IP

⸻

8️⃣ Create Web Pages
EC2 Instance 1
echo "<h1>SERVER 1</h1>" > index.html
EC2 Instance 2
echo "<h1>SERVER 2</h1>" > index.html

⸻

9️⃣ Start Python HTTP Server
sudo python3 -m http.server 80
OR background mode:
nohup sudo python3 -m http.server 80 &

⸻

🔟 Create Target Group
Target Group Configuration:
Setting	Value
Protocol	HTTP
Port	80
Health Check Path	/
Registered Targets:
* EC2 Instance 1
* EC2 Instance 2

⸻

1️⃣1️⃣ Create Application Load Balancer
ALB Configuration:
Setting	Value
Type	Internet Facing
Listener	HTTP : 80
Subnets	Public Subnets
Purpose:
* Distribute incoming traffic across backend servers.

⸻

1️⃣2️⃣ Verify Health Checks
Verified:
Healthy
for both EC2 instances.

⸻

1️⃣3️⃣ Test Load Balancing
Opened:
http://ALB-DNS-NAME
Browser responses alternated between:
SERVER 1
and
SERVER 2
This confirmed:
* Load balancing
* Round Robin traffic distribution
* High availability

⸻

🚨 Common Issues Faced & Fixes
SCP Syntax Error
Issue
No such file or directory
Fix
scp -i aws-login.pem aws-login.pem ubuntu@PUBLIC-IP:~

⸻

SSH Permission Denied
Issue
Permission denied (publickey)
Fix
chmod 400 aws-login.pem

⸻

502 Bad Gateway
Root Cause
ALB could not reach backend application.
Fix
Started Python server inside EC2:
sudo python3 -m http.server 80

⸻

Unhealthy Target Group
Root Cause
* HTTP port blocked
* Health check failure
Fix
Allowed:
HTTP 80 → ALB Security Group

⸻

Address Already In Use
Error
OSError: [Errno 98] Address already in use
Fix
sudo pkill -f http.server

⸻

Browser Showing Same Server
Root Cause
Browser cache.
Fix
* Hard refresh
* Incognito mode
* Different HTML pages

⸻

📊 Final Result
✔ VPC Configured ✔ Public & Private Subnets Created ✔ Internet Gateway Attached ✔ Route Tables Configured ✔ Security Groups Configured ✔ Bastion Host Configured ✔ Private EC2 Instances Running ✔ Python Web Servers Running ✔ Application Load Balancer Running ✔ Target Group Healthy ✔ Health Checks Passed ✔ Browser Accessible ✔ Traffic Distributed Between Both EC2s ✔ High Availability Achieved ✔ Auto Scaling Concept Understood

⸻

🧠 Key Learnings
Topic	Status
VPC Networking	✅
Route Tables	✅
Security Groups	✅
SSH & SCP	✅
Load Balancer	✅
Target Groups	✅
Health Checks	✅
Linux Troubleshooting	✅
High Availability	✅
Traffic Distribution	✅
⸻

🚀 Future Improvements
Planned enhancements:
* NGINX Reverse Proxy
* Docker Containers
* HTTPS using ACM
* Route53
* Terraform Automation
* CI/CD Pipeline
* Kubernetes Deployment

⸻

👨‍💻 Author
Created as part of AWS + DevOps hands-on learning journey.
