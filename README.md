# 2-tier-flaskapp
DevOps project: Containerized Flask + MySQL app deployed on AWS EC2 using Docker &amp; Compose, showcasing networking, automation &amp; scalability.


This project demonstrates the deployment of a two-tier application using:

Frontend/App Layer: Flask (Python)
Backend Layer: MySQL
Containerization: Docker & Docker Compose
Infrastructure: AWS EC2 (Ubuntu)

The application is containerized and deployed using Docker with a custom network for communication between services.

**Architecture**

Flask app connects to MySQL database
Both services run in separate containers
Docker network enables internal communication

**Prerequisites**

AWS EC2 instance (Ubuntu - Free Tier)
SSH client (MobaXterm / Terminal)
Docker & Docker Compose installed
Git installed

# EC2 Setup
launch ec2 with t2 micro and UBUNTU ami

# Connect to EC2 using SSH
chmod 400 <key.pem>

# Update packages
sudo apt-get update

# Verify OS
cat /etc/*release*

# Install Docker
sudo apt install docker -y
sudo docker run hello-world

# Fix permission issue
sudo chown ubuntu /var/run/docker.sock

# Clone Repository
git clone https://github.com/Naveen-5288/two-tier-flask-app.git
cd two-tier-flask-app/

# Build Docker Image
docker build . -t flaskapp

# Create Docker Network
docker network create 2-tier

# Run MySQL Container
docker run -d -p 3306:3306 \
--network=2-tier \
-e MYSQL_DATABASE=myDb \
-e MYSQL_USER=admin \
-e MYSQL_PASSWORD=admin \
-e MYSQL_ROOT_PASSWORD=admin \
--name=mysql mysql:5.7

# Run Flask App Container
docker run -d -p 5000:5000 \
--network=2-tier \
-e MYSQL_HOST=mysql \
-e MYSQL_USER=admin \
-e MYSQL_PASSWORD=admin \
-e MYSQL_DB=myDb \
--name=flaskapp flaskapp:latest

# Verify Containers
docker ps -a

# Database Setup
docker exec -it <mysql_container_id> bash

mysql -u admin -p

SHOW DATABASES;
USE myDb;

CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT
);

# Access Application

Open in browser:

http://<EC2-Public-IP>:5000

# Push Image to Docker Hub
docker login

docker tag flaskapp:latest naveenharikoppa/flaskapp:latest
docker push naveenharikoppa/flaskapp:latest

# Docker Compose Setup
sudo apt install docker-compose -y

# Create compose file
vi docker-compose.yaml

# Run services
docker-compose up -d

# Key Learnings
Containerization using Docker
Multi-container application setup
Docker networking
AWS EC2 deployment
Image push to Docker Hub
Docker Compose orchestration
