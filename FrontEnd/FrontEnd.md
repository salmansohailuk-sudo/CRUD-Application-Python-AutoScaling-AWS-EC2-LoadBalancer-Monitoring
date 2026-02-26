****Front End**

🚀 PART 6 — FRONTEND EC2 (Private)

Launch Front End EC2:

SSH via Bastion AND connect to FRONT END SERVER.

Install Nginx:

sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

🚀 FRONTEND FILE

sudo vi /usr/share/nginx/html/index.html

Front EC2 
#BASH 

🚀 ADD REVERSE PROXY
sudo vi /etc/nginx/conf.d/app.conf

Final Step - Restart:

sudo nginx -t
sudo systemctl restart nginx
sudo systemctl reload nginx
