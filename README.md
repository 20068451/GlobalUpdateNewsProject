# 📂 README.md: GlobalUpdateNewsApp Infrastructure Automation Project - Detailed Guide

This repository contains all the configuration files, scripts, and commands required to automatically deploy the **GlobalUpdateNewsApp** web application onto **Amazon Web Services (AWS)** using a full DevOps pipeline.

This document serves as the **comprehensive guide** for the project submission in the Network Systems and Administration Continuous Assessment.

---

## 1. Project Overview & Architecture 🏗️

The project successfully automates the deployment of a containerized web server on AWS using **Infrastructure-as-Code (IaC)** and **Continuous Integration/Continuous Delivery (CI/CD)**.

| Component | Role in Project | Technical Detail |
| :--- | :--- | :--- |
| **Cloud Platform** | AWS | Region used: `eu-west-1` (Ireland). |
| **IaC Tool** | Terraform (v1.13.4) | Used to provision 8 AWS resources (VPC, Subnet, EC2, etc.). |
| **Containerization** | Docker (v28.5.1) | Packages the application using `nginx:alpine` and exposes Port 80. |
| **CI/CD** | GitHub Actions | Automates the **Build**, **Push**, and **Deployment** steps. |
| **Application** | Global Update News App | Deploys the application image tagged as `20068451/globalupdatenewsapp:latest`. |

### Architecture Diagram
The system automatically handles the flow from code change to live deployment.



---

## 2. Deliverables and Key Artifacts

All required files are located within this repository:

| Component | File/Location | Purpose |
| :--- | :--- | :--- |
| **Infrastructure** | `terraform/main.tf` | **Provisions** all necessary AWS resources and outputs the Public IP. |
| **Application Code** | `app/index.html` | Contains the web page content for the news application. |
| **Dockerfile** | `Dockerfile` | Defines the container image build process. |
| **CI/CD Pipeline** | `.github/workflows/ci-cd.yml` | YAML definition of the deployment workflow. |

**External Links:**

* **GitHub Repository:** `https://github.com/20068451/GlobalUpdateNewsProject`
* **Video Demo Link:** `https://mydbs-my.sharepoint.com/:v:/g/personal/20068451_mydbs_ie/EZCa7wCyZ55AnrbbV9_pKqABlbBhWUiM5GIxrQ-GKfjZhA?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=zG29GS`

---

## 3. Infrastructure Details (Terraform)

The `main.tf` file defines the AWS environment:

### Networking Configuration
* **VPC CIDR:** `10.0.0.0/16`.
* **Public Subnet CIDR:** `10.0.1.0/24`.
* **Security Group (SG) Rules:**
    * **Inbound (Ingress):** Allows TCP Port **22** (SSH) and TCP Port **80** (HTTP) from `0.0.0.0/0`.
    * **Outbound (Egress):** Allows all traffic to `0.0.0.0/0`.
* **Key Pair:** Uses the local public key file (`~/.ssh/id_ed25519.pub`).

### EC2 Instance Configuration
* **AMI:** `ami-0bc691261a82b32bc` (Ubuntu image).
* **Instance Type:** `t3.micro`.

### Server Configuration Script (`user_data`)
This is the shell script executed upon EC2 launch to install Docker and prerequisites:

```bash
#!/bin/bash
set -eux
apt-get update -y
apt-get install -y docker.io
systemctl enable --now docker
usermod -aG docker ubuntu

