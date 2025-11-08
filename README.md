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
## External Links

* **GitHub Repository:** [GlobalUpdateNewsProject](https://github.com/20068451/GlobalUpdateNewsProject)
* **Video Demo Link:** [Project Video Demo](https://mydbs-my.sharepoint.com/:v:/g/personal/20068451_mydbs_ie/EZCa7wCyZ55AnrbbV9_pKqABlbBhWUiM5GIxrQ-GKfjZhA?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=zG29GS)

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
All Terraform HCL code is located in the `terraform/main.tf` file. This file defines the infrastructure shown above, including the network setup, Security Group rules, and the `user_data` script.

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
The complete CI/CD pipeline config is added in the **`.github/workflows/ci-cd.yml`** file.

### Push your project to GitHub
git add .
git commit -m "Initial commit – Global Update News App Setup"
git remote add origin git@github.com:20068451/GlobalUpdateNewsProject.git
git remote -v
git push -u origin main

### Check on the GitHub Website about workflow.

### Check html http:// EC2 Instance Public IP
---
