# Student-CRUD-Docker

## 📌 Project Overview

Student-CRUD-Docker is a 3-tier Student Management CRUD application deployed using Docker.  
It consists of a React frontend, Spring Boot backend, and AWS RDS (MariaDB) database, connected using Docker networking and Nginx reverse proxy.

This project demonstrates real-world containerized deployment on AWS EC2.

---

## 🏗️ Architecture

Browser  
⬇  
React + Nginx (Frontend Container)  
⬇  
Spring Boot API (Backend Container)  
⬇  
AWS RDS (MariaDB)

All containers communicate using a custom Docker network.

---

## 🛠️ Tech Stack

- Frontend: React + Nginx
- Backend: Spring Boot (Java 17)
- Database: AWS RDS (MariaDB)
- Containerization: Docker
- Cloud: AWS EC2
- Networking: Docker Bridge Network

---

## 🚀 Features

- Register Student
- View Student List
- Update & Delete Records
- REST API Integration
- Reverse Proxy with Nginx
- RDS Database Connectivity
- Dockerized Deployment

---

## ⚙️ Prerequisites

- AWS EC2 (Ubuntu)
- Docker Installed
- AWS RDS (MariaDB)
- GitHub Account

---

## 📦 Step 1: Install Docker on EC2

```bash
sudo apt update
sudo apt install ca-certificates curl -y

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
-o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io \
docker-buildx-plugin docker-compose-plugin -y

Verify:

docker --version
📂 Step 2: Clone Project
git clone https://github.com/mukundDeo9325/EasyCRUD.git
cd EasyCRUD
🏗️ Step 3: Build Backend Image

Edit configuration:

cd backend
vim src/main/resources/application.properties

Example:

spring.datasource.url=jdbc:mariadb://<RDS-ENDPOINT>:3306/student_db
spring.datasource.username=admin
spring.datasource.password=********

Build:

docker build -t backend:latest .
🌐 Step 4: Build Frontend Image
cd ../frontend
docker build --no-cache -t frontend:latest .
🔗 Step 5: Create Docker Network
docker network create app-net

Verify:

docker network ls
▶️ Step 6: Run Backend Container
docker run -d \
 --name backend \
 --network app-net \
 -p 8080:8080 \
 -e DB_HOST="<RDS-ENDPOINT>" \
 -e DB_NAME="student_db" \
 -e DB_USER="admin" \
 -e DB_PASS="password" \
 backend:latest

Check:

docker ps
docker logs backend
▶️ Step 7: Run Frontend Container
docker run -d \
 --name frontend \
 --network app-net \
 -p 80:80 \
 frontend:latest

Check:

docker ps
docker logs frontend
🔁 Step 8: Nginx Reverse Proxy Configuration

Frontend Nginx is configured to forward API requests:

location /api/ {
    proxy_pass http://backend:8080/;
}

This enables frontend to communicate with backend using Docker DNS.

🌍 Step 9: Access Application

Open in browser:

http://<EC2-PUBLIC-IP>

Example:

http://13.201.52.31
🧪 Step 10: Testing

Check backend:

curl http://localhost:8080/api/students

Check frontend:

curl http://localhost

Check container network:

docker exec -it frontend ping backend
🛡️ AWS Security Group

Inbound Rules:

Type	Port	Source
HTTP	80	0.0.0.0/0
SSH	22	Your IP
🐞 Troubleshooting
Port Not Working
sudo netstat -tulnp | grep :80
Restart Docker
sudo systemctl restart docker
View Logs
docker logs backend
docker logs frontend
Clean Containers
docker rm -f frontend backend
docker system prune -f
