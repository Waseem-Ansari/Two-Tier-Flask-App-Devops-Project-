# Two-Tier-Flask-App-Devops-Project-
Automated CI/CD Pipeline for a 2-Tier Flask Application on AWS

Project Overview 

Deploying a 2-tier web application (Flask + MySQL) on an AWS EC2 instance. The deployment is containerized using Docker and Docker Compose. A full CI/CD pipeline is established using Jenkins to automate the build and deployment process whenever new code is pushed to a GitHub repository.

Architecture Diagram

+-----------------+      +----------------------+      +-----------------------------+
|   Developer     |----->|     GitHub Repo      |----->|        Jenkins Server       |
| (pushes code)   |      | (Source Code Mgmt)   |      |  (on AWS EC2)               |
+-----------------+      +----------------------+      |                             |
                                                       | 1. Clones Repo              |
                                                       | 2. Builds Docker Image      |
                                                       | 3. Runs Docker Compose      |
                                                       +--------------+--------------+
                                                                      |
                                                                      | Deploys
                                                                      v
                                                       +-----------------------------+
                                                       |      Application Server     |
                                                       |      (Same AWS EC2)         |
                                                       |                             |
                                                       | +-------------------------+ |
                                                       | | Docker Container: Flask | |
                                                       | +-------------------------+ |
                                                       |              |              |
                                                       |              v              |
                                                       | +-------------------------+ |
                                                       | | Docker Container: MySQL | |
                                                       | +-------------------------+ |
                                                       +-----------------------------+

Step 1: AWS EC2 Instance Preparation
Launch EC2 Instance:
Navigate to the AWS EC2 console.
Launch a new instance using the Ubuntu 24.04 LTS AMI.
Select the t2.micro instance type for free-tier eligibility.
Create and assign a new key pair for SSH access.

<img width="1906" height="812" alt="image" src="https://github.com/user-attachments/assets/ee5ac38b-9d0a-416c-a72d-932a641fdbfb" />

Configure Security Group:
Create a security group with the following inbound rules:
Type: SSH, Protocol: TCP, Port: 22
Type: HTTP, Protocol: TCP, Port: 80 
Type: Custom TCP, Protocol: TCP, Port: 5000 (for Flask)
Type: Custom TCP, Protocol: TCP, Port: 8080 (for Jenkins)

<img width="1575" height="753" alt="image" src="https://github.com/user-attachments/assets/3e0d799e-ca62-4660-bbac-a99b53235967" />

Connect to EC2 Instance:
Use SSH to connect to the instance's public IP address.
ssh -i /path/to/key.pem ubuntu@<ec2-public-ip>

--------------------------------------------------------------------------------------------------------------------------------------
Step 2: Install Dependencies on EC2

 1.Update System Packages:
   sudo apt update && sudo apt upgrade -y

 2.Install Git, Docker, and Docker Compose:
   sudo apt install git docker.io docker-compose-v2 -y

 3.Start and Enable Docker:
   sudo systemctl start docker
   sudo systemctl enable docker

 4.Add User to Docker Group (to run docker without sudo):
   sudo usermod -aG docker $USER
   newgrp docker

--------------------------------------------------------------------------------------------------------------------------------------
Step 3: Jenkins Installation and Setup
        Install Java (OpenJDK 17):
        sudo apt install openjdk-17-jdk -y

Add Jenkins Repository and Install:
curl -fsSL [https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key](https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key) | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] [https://pkg.jenkins.io/debian-stable](https://pkg.jenkins.io/debian-stable) binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y

Start and Enable Jenkins Service:
sudo systemctl start jenkins
sudo systemctl enable jenkins

Initial Jenkins Setup:
Retrieve the initial admin password:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Access the Jenkins dashboard at http://<ec2-public-ip>:8080.
Paste the password, install suggested plugins, and create an admin user.

Grant Jenkins Docker Permissions:
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

<img width="1897" height="815" alt="image" src="https://github.com/user-attachments/assets/c85a39c3-9003-4255-a8b7-691c30cc3677" />

--------------------------------------------------------------------------------------------------------------------------------------
