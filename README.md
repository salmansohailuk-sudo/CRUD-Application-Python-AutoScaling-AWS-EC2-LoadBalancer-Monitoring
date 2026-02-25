

# CRUD-Application-Python-AutoScaling-AWS-EC2-LoadBalancer-Monitoring

a production-grade Flask CRUD application on EC2 instances behind an Application Load Balancer. The app runs with Gunicorn, Nginx acts as a reverse proxy, RDS 

MySQL stores persistent data, and Auto Scaling ensures high availability and scalability.

**ARCHITECTURE**
<img width="1041" height="995" alt="roundtrip" src="https://github.com/user-attachments/assets/a68043e0-2450-4b66-8376-a83c8cac0bc8" />


Traffic Flow

User
→ ALB (Public Subnet)
→ Frontend Create EC2 Frontend-EC2  (Private Subnet, Nginx)
→ Backend Create EC2 Backend-EC2 (Private Subnet, Gunicorn)
→ RDS (Private Subnet)

Admin
→ Bastion Host
→ Private Servers

Uses:

Amazon Virtual Private Cloud

Elastic Load Balancing

Amazon Elastic Compute Cloud

Amazon Relational Database Service

PART 1 — NETWORK SETUP
STEP 1 — Create VPC

CIDR:

10.0.0.0/16

STEP 2 — Create Subnets

**Create Public Subnets x 2**

10.0.1.0/24 (ALB + Bastion)

10.0.2.0/24 (ALB HA)

**Private App Subnets**

10.0.3.0/24 (Frontend)

10.0.4.0/24 (Backend)

**Private RDS-Subnets x 2**

10.0.5.0/24

10.0.6.0/24

STEP 3 — Internet Gateway

Create  DEV-IG

Attach to VPC

**Public route table:**
Public-rt

0.0.0.0/0 → IGW

Associate to public subnets.

STEP 4 — NAT Gateway


Create NAT in public subnet

Private route table:

0.0.0.0/0 → NAT

Associate to all private subnets.  no rds ssubnets

**PART 2 — SECURITY GROUPS**

**Create SG-ALB**

Inbound:

80 → 0.0.0.0/0

**SG-Bastion**

Inbound:

22 → Your IP only

**SG-Frontend**

Inbound:

80 → SG-ALB

22 → SG-Bastion

**SG-Backend**

Inbound:

8000 → SG-Frontend

22 → SG-Bastion

**SG-RDS**

Inbound:

3306 → SG-Backend

**PART 3 — RDS SETUP**

Amazon Relational Database Service

Engine: MySQL

Public access: NO

Subnet group: DB private subnets

**PART 4 — BASTION HOST (Public)**

Launch EC2:
Public subnet

Attach SG-Bastion

Enable public IP

SSH:

ssh -i key.pem ec2-user@<bastion-public-ip>
PART 5 — BACKEND EC2 (Private)

**Create EC2 Backend Server**

Attached SG-Backend**
Private subnet 10.0.4.0/24

No public IP

ssh -i key.pem ec2-user@<backendServer-ip>

Install

yum install mariadb106 -y
mysql -h database-2.ca3m82g209lz.us-east-1.rds.amazonaws.com -u admin -pCloud123

**Inside Mysql**

CREATE DATABASE testdb;

USE testdb;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);


**Create RDS DATABASE**
**Attach SG-RDS**
After creation, note endpoint.


=================================


Backend Installation Instrucitons

OpenCheck Backend file 
=================================

Frontend Installation Instrucitons

OpenCheck Frontend file 
=================================


🚀 Create ALB and Target Group
=================================

👉 Elastic Load Balancing

Target group → Frontend EC2

Health check path: /

Port 80

**ALB Internet-facing**

Attach to Public subnets x 2

Attach SG-ALB

Target group → Frontend EC2

Health check path: /

Port 80

🚀 FINAL TEST - Open your Web Browser
=================================


http://ALB-DNS

Any issues, see below Knowledge Base and Trouble Shooting Steps or contact me **eMail: salmansohailuk@gmail.com Mobile Uk + 447356261997.**


🚀 Knowledge Base and Trouble Shooting Steps:
=================================


**Backend**

**Step 1 — Confirm If any issues with Backend (Not Nginx)**

SSH into Backend EC2 and run:

**##BASH**

INSERT record to your RDS Database and see if this get added

curl -X POST http://localhost:8000/users -H "Content-Type: application/json" -d '{"name":"test","email":"test@test.com"}'

If the above command retursn 500 → backend problem is confirmed not nginx.

To display all records from database or newly inserted record from above.

**#Bash - Run**

curl http://localhost:8000/users

To kill 8000 listening port

**#BASH**

sudo fuser -k 8000/tcp

**##Re-Run Gunicorn**

gunicorn -w 4 -b 0.0.0.0:8000 app:app

**###Telnet RDS**

telnet RDS-ENDPOINT 3306



====================End TroubleShooting.....


🚀 Future Enhancements IN progress:
=================================

Auto Scalling

Monitoring will be added.

Objectives:
Explore Montioring with Anil Jadhav

Splunk - Sales Effects - No of hits - no of successfull sales/registration no of failed or pending - payments how many comes and why this is failed. (Anil)
Grafana
DynaTrace - Smaller Scale Organization
App Dynamics - EnterPrise 

Regulator -  Rakesh to input to explore - Signal

