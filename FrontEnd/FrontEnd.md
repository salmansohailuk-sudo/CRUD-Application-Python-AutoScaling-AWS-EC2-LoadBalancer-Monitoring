# 🚀 Part 6 — Frontend EC2 (Private)

This guide walks you through launching your frontend EC2 instance, installing Nginx, creating frontend files, setting up a reverse proxy, and restarting Nginx.

---

## 1️⃣ Launch Frontend EC2
---

<summary>1️⃣ Launch Frontend EC2 🔑</summary>

- SSH via Bastion and connect to the **Frontend Server**.

```bash
## Example:
ssh -i your-key.pem ec2-user@bastion-host
ssh -i your-key.pem ec2-user@frontend-ec2-private-ip


---

## 2️⃣ Install Nginx

```bash
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx


sudo vi /usr/share/nginx/html/index.html

sudo vi /etc/nginx/conf.d/app.conf


sudo nginx -t            # Test Nginx config
sudo systemctl restart nginx
sudo systemctl reload nginx


