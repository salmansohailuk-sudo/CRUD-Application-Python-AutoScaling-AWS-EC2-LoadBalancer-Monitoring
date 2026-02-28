#  Reverse Proxy Explanation

What Is a Reverse NGINX Proxy?

A reverse proxy is a server that sits in front of one or more backend servers and forwards client requests to them.

When we say “reverse NGINX proxy”, we mean using NGINX as that reverse proxy.

🔁 How It Works (Simple Flow)
4

A user sends a request (example: visit example.com)

NGINX receives the request first

NGINX forwards the request to a backend server (like a Node.js, Python, or PHP app)

The backend sends the response back to NGINX

NGINX sends the response to the user

The user never talks directly to the backend server.

🔍 Why Use NGINX as a Reverse Proxy?
1️⃣ Load Balancing

Distributes traffic across multiple backend servers.

2️⃣ SSL Termination

Handles HTTPS encryption so backend servers don’t need to.

3️⃣ Security

Hides internal server IPs and can block malicious traffic.

4️⃣ Caching

Can cache responses to improve speed.

5️⃣ Centralized Routing

Route different URLs to different services:

/api → backend API server

/images → image server

/ → frontend app

🔄 Reverse Proxy vs Forward Proxy
Reverse Proxy	Forward Proxy
Protects servers	Protects clients
Used by websites	Used inside company networks
Users don’t know it exists	Users configure it manually
🛠 Simple Example Configuration
server {
    listen 80;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

This tells NGINX:

Listen on port 80

Forward all traffic to an app running on port 3000

💡 Real-World Example

If you use:

Docker containers

Microservices

Cloud servers

Kubernetes

You almost always use a reverse proxy like NGINX in front.


