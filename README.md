# 🌍 Global Update News App — Automated Cloud Infrastructure and CI/CD Deployment

This repository contains the full implementation of the **Global Update News App**, developed as part of the *B9IS121 Networks and Systems Administration* module.  
It demonstrates a complete **DevOps automation pipeline** using **Terraform**, **Docker**, **GitHub Actions**, and **Amazon Web Services (AWS)**.

---

## 🧾 Project Overview

The **Global Update News App** is a simple web application deployed on the AWS cloud using modern automation tools.  
The goal of the project was to design and implement a **fully automated infrastructure** that can:

- Provision AWS cloud resources using **Terraform**
- Containerize the application with **Docker**
- Automate build and deployment through **GitHub Actions**
- Host a live web application on **AWS EC2**, accessible via HTTP port 80

This project showcases the integration of **cloud networking, Infrastructure as Code, containerization, and CI/CD pipelines** within a single automated system.

---

## 🧰 Tools and Technologies

| Category | Tool | Description |
|-----------|------|-------------|
| **Cloud Platform** | AWS | Provides virtual servers (EC2), networking (VPC), and IAM security |
| **Infrastructure as Code (IaC)** | Terraform | Automates provisioning of AWS resources |
| **Containerization** | Docker | Packages the web application for portable deployment |
| **Version Control & CI/CD** | GitHub + GitHub Actions | Automates image builds and deployments |
| **Web Server** | NGINX | Hosts the static HTML web page within the Docker container |
| **Operating System** | Ubuntu 22.04 | Base OS for the EC2 instance |

---

## 🧠 Objectives

1. Automate AWS infrastructure deployment using **Terraform**.  
2. Configure networking with **VPC, Subnet, Security Group, and Internet Gateway**.  
3. Containerize the application with **Docker** and host it on **AWS EC2**.  
4. Set up a **CI/CD pipeline** using **GitHub Actions** for continuous delivery.  
5. Make the web app accessible to the public through port 80.  

---

## 🏗️ Infrastructure Architecture

### ⚙️ Key Components
- **VPC (10.0.0.0/16):** Creates a secure network boundary.
- **Subnet (10.0.1.0/24):** Provides public IP accessibility.
- **Internet Gateway:** Enables outbound access to the internet.
- **Route Table:** Directs traffic from the subnet to the internet.
- **Security Group:** Allows SSH (port 22) and HTTP (port 80).
- **EC2 Instance:** Hosts the Docker container.
- **Docker Hub:** Stores the built image for deployment.
- **GitHub Actions:** Automates build, push, and deploy.

---

## 🧩 System Workflow
Developer → GitHub Repository → GitHub Actions
↓ ↓
Docker Build & Push → Docker Hub
↓
Terraform Infrastructure (AWS)
↓
EC2 Instance (Ubuntu + Docker)
↓
Public User Access via HTTP

### 🧭 Workflow Explanation
1. **Developer Stage:** Code changes are pushed to GitHub.  
2. **Build Stage:** GitHub Actions builds the Docker image and uploads it to Docker Hub.  
3. **Infrastructure Stage:** Terraform provisions AWS VPC, subnet, security group, and EC2 instance.  
4. **Deployment Stage:** GitHub Actions connects to EC2 via SSH, pulls the new Docker image, and runs the container.  
5. **User Access:** Users access the live app at the EC2 public IP address through port 80.

---

## 🧱 Project Structure
GlobalUpdateNewsApp/
│
├── app/
│ └── index.html # Simple HTML web page
│
├── terraform/
│ └── main.tf # Terraform configuration
│
├── Dockerfile # Defines NGINX container setup
│
├── .github/
│ └── workflows/
│ └── ci-cd.yml # GitHub Actions pipeline
│
└── README.md # Project documentation

---

## 🚀 Deployment Steps

### Detail Steps
Step 1: Check Environment
```bash
terraform --version
docker --version
aws --version

Step 2: Configure AWS Credentials
aws configure
# Region: eu-west-1
# Output: json

Step 3: Initialize Terraform
cd terraform
terraform init
terraform plan
terraform apply -auto-approve

Step 4: Build and Push Docker Image
sudo docker build -t 20068451/globalupdatenewsapp:latest .
sudo docker login
sudo docker push 20068451/globalupdatenewsapp:latest

Step 5: Configure GitHub Secrets
| Name              | Description                             |
| ----------------- | --------------------------------------- |
| `DOCKER_USERNAME` | Your Docker Hub username                |
| `DOCKER_PASSWORD` | Your Docker Hub token                   |
| `EC2_PUBLIC_IP`   | EC2 Public IP (from Terraform output)   |
| `SSH_PRIVATE_KEY` | Private key used for EC2 SSH connection |

Step 6: GitHub Actions CI/CD Workflow
name: Build and Deploy to EC2

on:
  push:
    branches: [ "main" ]

jobs:
  build_and_push:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: 20068451/globalupdatenewsapp:latest

  deploy:
    needs: build_and_push
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to EC2 via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.EC2_PUBLIC_IP }}
          username: ubuntu
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            sudo systemctl enable --now docker || true
            docker pull 20068451/globalupdatenewsapp:latest
            docker rm -f globalupdatenews || true
            docker run -d -p 80:80 --name globalupdatenews 20068451/globalupdatenewsapp:latest

Step 7: Verify the Deployment

After a successful GitHub Actions run:

Visit your EC2 public IP → http://108.130.52.133

You’ll see:
“Global Update News App — Development Testing Interface”

Check container status:
ssh -i ~/.ssh/id_ed25519 ubuntu@108.130.52.133
docker ps

Testing and Validation

Pushed new HTML code to the GitHub main branch.

GitHub Actions automatically rebuilt the image and deployed it.

Accessing the EC2 IP displayed the updated version of the website.

Confirmed continuous delivery through automatic pipeline triggers.



