# Cloud Web Server Deployment with Terraform and Ansible using DevOps best practices
## AWS + Terraform + Ansible + GitHub Actions 

**By:** Arinze Akosa
**Project:** Automated Container deployment and Administration in the Cloud
**Module:** Network Systems & Administration

---

## Overview

This project implements automated deployment of a secure web server on AWS EC2 through **Terraform** for infrastructure provisioning and **Ansible** for Docker-based application deployment and configuration management.

---

## Project Components

The system comprises different parts that function according
to the following description.

| Component | Purpose |
| :--- | :--- |
| Terraform | AWS resources including EC2 instance with Security Group and Key Pair are provisioned using terraform. |
| Ansible | Ansible deploys Docker and Nginx on the servers while handling web application deployment. |
| Docker | The EC2 host runs web applications through Docker container management. |
| Nginx | The Nginx reverse proxy transfers incoming port 80 traffic to the application container listening on port 8080. |
| GitHub Actions | CI/CD pipeline for automated deployment. |

---

## AWS Infrastructure

The infrastructure operates in the **us-west-2 (Oregon)** region.

### EC2 Instance (arinze_server)

* Instance Type: `t3.micro`
* AMI: `ami-0c5204531f799e0c6` (Amazon Linux 2)
* The instance connects to the public internet through its public IP address.

### Security Group (‘arinze_security_group’)

All inbound traffic is permitted through the following ports from any source address `0.0.0.0/0` according to the system security group rules:

* HTTP: Port 80
* SSH: Port 22 (This is restricted in a production environment)
* HTTPS: Port 443

---

## Configuration (Ansible ‘deploy.yml’)

The Ansible playbook utilizes the **local-exec provisioner** from Terraform for command execution as follows:

* The system requires dependencies like **Python version 3.8** to be installed when setting up the EC2 instance.
* The system first installs **Docker** and activates its service before adding `ec2-user` to the Docker group and then installs **Docker Compose version 2.20.0**.
* The system uses **Nginx installation** to establish reverse proxy functionality which sends all port 80 traffic to `http://localhost:8080`.
* The deployment operation performs file transfer operations first before launching the **Docker Compose process** to create the web application container on the EC2 host.

---

## CI/CD Pipeline (GitHub Actions)

The `deploy.yml` workflow activates in response to either **main branch pushes** or **pull request creation**.

### Key Steps
Setup SSH Keys:** Private and public keys are generated from repository secrets (`SSH_PRIVATE_KEY` and `SSH_PUBLIC_KEY`) to enable Terraform Key Pair creation and Ansible SSH access.
The deployment process executes when the **main branch detects a push** with the `-auto-approve` flag.

The workflow maintains the **`terraform.tfstate`** file by passing it between download and upload operations through artifacts.

### Required GitHub Secrets

All GitHub repositories need the following secrets to be configured:

| Secret Name | Description |
| :--- | :--- |
| `AWS_ACCESS_KEY_ID` | My AWS Access Key ID. |
| `AWS_SECRET_ACCESS_KEY` | My AWS Secret Access Key. |
| `SSH_PRIVATE_KEY` | The content of the private key file (e.g., arinze). |
| `SSH_PUBLIC_KEY` | The content of the public key file (e.g., arinze.pub). |

---

## Accessing the Application

Users can reach the web server through **standard HTTP (Port 80)** while using the EC2 instance's public IP.

Terraform outputs the public IP address of the instance along with other information following a successful apply operation:

# Terraform Output
The output shows the instance public IP address as "34.211.7.156” under the instance public ip field.


