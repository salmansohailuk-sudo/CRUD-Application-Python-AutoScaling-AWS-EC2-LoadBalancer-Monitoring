

<img width="937" height="720" alt="aws_architecture" src="https://github.com/user-attachments/assets/3c6d028f-9156-4e41-8b3e-45894c668a27" />

# CRUD-Application-Python-AutoScaling-AWS-EC2-LoadBalancer-Monitoring

a production-grade Flask CRUD application on EC2 instances behind an Application Load Balancer. The app runs with Gunicorn, Nginx acts as a reverse proxy, RDS MySQL stores persistent data, and Auto Scaling ensures high availability and scalability.

ARCHITECTURE
4
Traffic Flow

User
→ ALB (Public Subnet)
→ Frontend EC2 (Private Subnet, Nginx)
→ Backend EC2 (Private Subnet, Gunicorn)
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
Public Subnets

10.0.1.0/24 (ALB + Bastion)

10.0.2.0/24 (ALB HA)

Private App Subnets

10.0.3.0/24 (Frontend)

10.0.4.0/24 (Backend)

Private DB Subnets

10.0.5.0/24

10.0.6.0/24

STEP 3 — Internet Gateway

Attach IGW to VPC
Public route table:

0.0.0.0/0 → IGW

Associate to public subnets.

STEP 4 — NAT Gateway

Create Elastic IP

Create NAT in public subnet

Private route table:

0.0.0.0/0 → NAT

Associate to all private subnets.

PART 2 — SECURITY GROUPS
SG-ALB

Inbound:

80 → 0.0.0.0/0

SG-Bastion

Inbound:

22 → Your IP only

SG-Frontend

Inbound:

80 → SG-ALB

22 → SG-Bastion

SG-Backend

Inbound:

8000 → SG-Frontend

22 → SG-Bastion

SG-RDS

Inbound:

3306 → SG-Backend

PART 3 — RDS SETUP

In:

Amazon Relational Database Service

Engine: MySQL

Public access: NO

Subnet group: DB private subnets

yum install mariadb106 -y
mysql -h database-2.ca3m82g209lz.us-east-1.rds.amazonaws.com -u admin -pCloud123


CREATE DATABASE testdb;

USE testdb;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);


Attach SG-RDS

After creation, note endpoint.

PART 4 — BASTION HOST (Public)

Launch EC2:

Public subnet

Attach SG-Bastion

Enable public IP

SSH:

ssh -i key.pem ec2-user@<bastion-public-ip>
PART 5 — BACKEND EC2 (Private)

Launch EC2:

Private subnet 10.0.4.0/24

No public IP

Attach SG-Backend

SSH via Bastion:

ssh ec2-user@<backend-private-ip>
🚀 BACKEND INSTALLATION
sudo yum update -y
sudo yum install python3 -y
pip3 install flask pymysql gunicorn

Create app folder:

mkdir app
cd app
nano app.py
🧩 BACKEND CODE

export DB_HOST=database-2.ca3m82g209lz.us-east-1.rds.amazonaws.com
export DB_USER=admin
export DB_PASS=Cloud123
export DB_NAME=te


from flask import Flask, request, jsonify
import pymysql, os

app = Flask(__name__)

def db():
    return pymysql.connect(
        host="database-2.ca3m82g209lz.us-east-1.rds.amazonaws.com",
        user="admin",
        password="Cloud123",
        database="testdb",
        cursorclass=pymysql.cursors.DictCursor,
        autocommit=True
    )


@app.route("/users", methods=["GET"])
def users():
    conn=db();cur=conn.cursor()
    cur.execute("SELECT * FROM users")
    data=cur.fetchall()
    conn.close()
    return jsonify(data)


async function add(){
 const nameField = document.getElementById("name");
 const emailField = document.getElementById("email");

 const nameValue = nameField.value.trim();
 const emailValue = emailField.value.trim();

 if(!nameValue || !emailValue){
   alert("Please fill both fields");
   return;
 }

 await fetch(API+"/users",{
   method:"POST",
   headers:{
     "Content-Type":"application/json"
   },
   body:JSON.stringify({
     name:nameValue,
     email:emailValue
   })
 });

 nameField.value="";
 emailField.value="";

 load();
}

@app.route("/users/<int:id>", methods=["DELETE"])
def delete(id):
    conn=db();cur=conn.cursor()
    cur.execute("DELETE FROM users WHERE id=%s",(id,))
    conn.commit();conn.close()
    return {"msg":"deleted"}

   
Replace RDS endpoint.

Create DB Table

SSH backend:

mysql -h RDS-ENDPOINT -u admin -p
CREATE DATABASE testdb;
USE testdb;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
🚀 RUN BACKEND
gunicorn -w 4 -b 0.0.0.0:8000 app:app

Test:

curl http://localhost:8000/users
🚀 PART 6 — FRONTEND EC2 (Private)

Launch EC2:

Private subnet 10.0.3.0/24

Attach SG-Frontend

SSH via Bastion.

Install Nginx:

sudo yum install nginx -y
🚀 FRONTEND FILE
sudo nano /usr/share/nginx/html/index.html
<!DOCTYPE html>
<html>
<body>
<h2>CRUD App</h2>
<input id="name" placeholder="Name">
<input id="email" placeholder="Email">
<button onclick="add()">Add</button>
<ul id="list"></ul>

<script>
const API="/api";

async function load(){
 let r=await fetch(API+"/users");
 let d=await r.json();
 let list=document.getElementById("list");
 list.innerHTML="";
 d.forEach(u=>{
 list.innerHTML+=`<li>${u.name}-${u.email}
 <button onclick="del(${u.id})">X</button></li>`;
 });
}

async function add(){
 const nameField = document.getElementById("name");
 const emailField = document.getElementById("email");

 const nameValue = nameField.value.trim();
 const emailValue = emailField.value.trim();

 if(!nameValue || !emailValue){
   alert("Please fill both fields");
   return;
 }

 await fetch(API+"/users",{
   method:"POST",
   headers:{
     "Content-Type":"application/json"
   },
   body:JSON.stringify({
     name:nameValue,
     email:emailValue
   })
 });

 nameField.value="";
 emailField.value="";
 load();
}

async function del(id){
 await fetch(API+"/users/"+id,{method:"DELETE"});
 load();
}

load();
</script>
</body>
</html>
 
  
  
  
  🚀 ADD REVERSE PROXY
sudo nano /etc/nginx/conf.d/app.conf
server {
    listen 80;

    # Serve frontend static files
    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ =404;
    }

    # Reverse proxy to backend
    location /api/ {
        proxy_pass http://10.0.3.36:8000/;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}


Restart:

sudo nginx -t
sudo systemctl restart nginx
🚀 PART 7 — CREATE ALB

In:

👉 Elastic Load Balancing

Internet-facing

Public subnets

Attach SG-ALB

Target group → Frontend EC2

Health check path: /

🚀 FINAL TEST

Open:

http://ALB-DNS

Now:

Browser → ALB → Frontend → Nginx /api → Backend → RDS

No private IP exposed.


**learnings:**

Step 1 — Confirm It’s Backend (Not Nginx)

SSH into Backend EC2 and run:

**Backend**
curl -X POST http://localhost:8000/users \
-H "Content-Type: application/json" \
-d '{"name":"test","email":"test@test.com"}'

If this also returns 500 → backend problem confirmed.


curl http://localhost:8000/users

sudo fuser -k 8000/tcp
gunicorn -w 4 -b 0.0.0.0:8000 app:app
telnet RDS-ENDPOINT 3306



=========================================

Future Enhancements
Monitoring will be added.

Enhancements:

Objectives:
Explore Montioring with Anil Jadhav

Splunk - Sales Effects - No of hits - no of successfull sales/registration no of failed or pending - payments how many comes and why this is failed. (Anil)
Grafana
DynaTrace - Smaller Scale Organization
App Dynamics - EnterPrise 

Regulator -  Rakesh to input to explore - Signal

