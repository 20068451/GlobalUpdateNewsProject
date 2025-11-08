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
# Global Update News App — End-to-End DevOps Automation with Terraform, Docker, GitHub Actions, and AWS

This repository contains the complete implementation of the Global Update News App, a project developed for the B9IS121 Networks and Systems Administration.  
The goal of this project is to demonstrate automation practices with Infrastructure as Code (IaC), Containerization, and Continuous Integration/Continuous Deployment (CI/CD) can be combined to create a fully automated cloud deployment pipeline.

---
## Summary

The Global Update News App is a simple web application deployed on the AWS Cloud using automation tools such as Terraform, Docker, and GitHub Actions.  
This project shows how infrastructure can be provisioned automatically, applications can be built as Docker containers, and updates can be continuously deployed without manual intervention.  

By using Terraform, the project creates and configures the AWS environment including a VPC, subnet, Internet Gateway, Security Group, and EC2 instance.  
Docker is used to package the HTML based web app into a container image, which is stored in my Docker Hub.  
Finally with the GitHub Actions automates the entire build and deployment process so that any code change pushed to the main branch is instantly reflected on the live production web server.

---
## External Links

GitHub Repository:`https://github.com/20068451/GlobalUpdateNewsProject`
Video Demo Link:`https://mydbs-my.sharepoint.com/:v:/g/personal/20068451_mydbs_ie/EZCa7wCyZ55AnrbbV9_pKqABlbBhWUiM5GIxrQ-GKfjZhA?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=zG29GS`

---
## Technologies and Tools

For this Global Update News App project,
Amazon Web Services (AWS) - Provides the hosting platform for EC2, VPC, IAM, and networking
Terraform - Automates the creation of AWS infrastructure resources
Docker - Packages the web application into a portable container image
GitHub - Automates build and deployment when html code is pushed to GitHub
Operating System - Ubuntu 22.04 LTS (EC2 instance OS used to host and run Docker containers)
Web Server - NGINX Serves static HTML content from the Docker container

---
## Project Objectives

My main goals for this project were:
Automate a cloud network using Terraform
Apply a cloud network with security rules and segments.
Run a Docker based containerized app on an AWS EC2 Instance server.
Setup with CI/CD system with GitHub Actions for automatic rebuilds when code changes.
Make the app accessible via public.
Steps by steps document, problems, and outcomes.

---
## Infrastructure Architecture
### AWS Components
- VPC (10.0.0.0/16)
- Subnet (10.0.1.0/24) 
- Internet Gateway (IGW)
- Route Table 
- Security Group 
- EC2 Instance 

---
## System Workflow
Terraform Infrastructure (AWS) → Create EC2 Instance (Ubuntu + Docker)
Developer → GitHub Repository → GitHub Actions → Docker Build & Push → Docker Hub → EC2 Instance → Public User Access via HTTP
### Workflow Explanation
1. Terraform code will create AWS infrastructure — VPC, subnet, route table, Internet Gateway, security group, and EC2 instance, all written in the `main.tf` file.   
2. Writes or updates the html code and pushes it to the GitHub repository.  
3. GitHub Actions pipeline detects a change in the main branch. It builds a Docker image from the project’s `Dockerfile` and pushes it to Docker Hub.  
4. GitHub Actions workflow connect to the EC2 instance via SSH, pull the latest image from Docker Hub, and restart the container automatically.  
5. Public user can access the EC2 Instance public IP address (e.g., http://108.130.52.133) in a browser to see the webpage.
All steps are fully automated, repeatable and version controlled.

---
## Detail Project Implementation Process

### Check Tools at WSL
terraform --version
docker --version
aws --version

### Configure AWS CLI
aws configure
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]: eu-west-1
Default output format [None]: json

### Generate SSH Key (Public key + Private key)
Securely logging into servers (AWS EC2 instance)
Authenticating with GitHub (to push and pull code)
GitHub Actions (workflow deploy to EC2 safely)

### Create Test HTML Webpage at Project Folder

### Create the Dockerfile

### Build your Docker image at Ubuntu Host
sudo docker build -t 20068451/globalupdatenewsapp:latest .

### Check your Docker image at Ubuntu Host
sudo docker images

### Docker Login
sudo docker login

### Push your image to Docker Repository
sudo docker push 20068451/globalupdatenewsapp:latest

### Create Terraform configuration file
terraform {
  required_version = ">= 1.6.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "eu-west-1"
}

resource "aws_key_pair" "this" {
  key_name   = "GlobalUpdateNewsApp"
  public_key = file("~/.ssh/id_ed25519.pub")
}

resource "aws_vpc" "this" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = { Name = "GlobalUpdateNewsAppVPC" }
}

resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id
  tags   = { Name = "GlobalUpdateNewsAppIGW" }
}

resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.this.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "eu-west-1"
  map_public_ip_on_launch = true

  tags = { Name = "GlobalUpdateNewsAppSubnet" }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.this.id
  }

  tags = { Name = "GlobalUpdateNewsAppRouteTable" }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

resource "aws_security_group" "web" {
  name        = "GlobalUpdateNewsApp-SG"
  description = "Allow SSH, HTTP"
  vpc_id      = aws_vpc.this.id

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = { Name = "GlobalUpdateNewsAppSecurityGroup" }
}


resource "aws_instance" "web" {
  ami                    = "ami-0bc691261a82b32bc"
  instance_type          = "t3.micro"
  key_name               = aws_key_pair.this.key_name
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web.id]

  tags = { Name = "GlobalUpdateNewsWebServer" }

  user_data = <<-EOF
    #!/bin/bash
    set -eux
    apt-get update -y
    apt-get install -y docker.io
    systemctl enable --now docker
    usermod -aG docker ubuntu
  EOF
}

output "vpc_id" {
  value       = aws_vpc.this.id
  description = "VPC ID"
}

output "security_group_id" {
  value       = aws_security_group.web.id
  description = "Security Group ID"
}

output "ec2_instance_id" {
  value       = aws_instance.web.id
  description = "EC2 Instance ID"
}

output "ec2_public_ip" {
  value       = aws_instance.web.public_ip
  description = "Public IPv4 address for the web instance"
}

### Terraform Apply
terraform init
terraform plan
terraform apply -auto-approve

### Connect to EC2 instance and Checking
ssh -i ~/.ssh/id_ed25519 ubuntu@Public IP address
docker --version
sudo docker run hello-world

### Create Repository on GitHub
Go to https://github.com/new
Repository name: https://github.com/20068451/GlobalUpdateNewsProject
Description: Infrastructure + CI/CD Deployment Project
Visibility: Public

### Add required GitHub Secrets
(repo → Settings → Secrets and variables → Actions)
DOCKER_USERNAME: Docker Hub ID
DOCKER_PASSWORD: Personal Access Token
EC2_PUBLIC_IP : Public IP address
SSH_PRIVATE_KEY : SECRET - Content of the private key

### Create the CI/CD workflow file at Ubuntu Host
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

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and push Docker image
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
          script_stop: true
          script: |
            set -eux
            sudo systemctl enable --now docker || true
            docker pull 20068451/globalupdatenewsapp:latest
            docker rm -f oneworld-web || true
            docker run -d --restart unless-stopped -p 80:80 --name globalupdatenews 20068451/globalupdatenewsapp:latest
            sleep 2
            curl -I http://localhost || true

### Push your project to GitHub
git add .
git commit -m "Initial commit – Global Update News App Setup"
git remote add origin git@github.com:20068451/GlobalUpdateNewsProject.git
git remote -v
git push -u origin main

### Check on the GitHub Website about workflow.

### Check html http:// 
---
