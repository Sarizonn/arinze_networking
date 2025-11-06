# Cloud Web Server Deployment with Terraform and Ansible

This project automates the deployment of a secure web server on **AWS EC2** using **Terraform** for infrastructure and **Ansible** for configuration and application deployment via Docker.

---

## Project Components

| Component | Purpose |
| :--- | :--- |
| **Terraform** | Provisioning AWS resources (EC2 instance, Security Group, Key Pair). |
| **Ansible** | Configuring the server (installing Docker, Nginx) and deploying the web application. |
| **Docker/Compose** | Containerizing and managing the web application on the EC2 host. |
| **Nginx** | Acts as a reverse proxy, forwarding port 80 traffic to the application container on port 8080. |
| **GitHub Actions** | CI/CD pipeline for automated deployment. |

---

## AWS Infrastructure

The infrastructure is deployed to the **`us-west-2`** (Oregon) region.

### EC2 Instance (`arinze_server`)

* **Instance Type:** `t3.micro`
* **AMI:** `ami-0c5204531f799e0c6` (Amazon Linux 2)
* **Networking:** Associated with a public IP address.

### Security Group (`arinze_security_group`)

Inbound traffic is allowed on the following ports from anywhere (`0.0.0.0/0`):

* **HTTP:** Port 80
* **SSH:** Port 22 (Note: Restrict this in a production environment)
* **HTTPS:** Port 443

---

## Configuration (Ansible `deploy.yml`)

The Ansible playbook executes as a local-exec provisioner in Terraform and performs the following steps:

1.  **Dependencies:** Installs **Python 3.8** on the EC2 instance.
2.  **Docker Setup:** Installs and starts **Docker** and installs **Docker Compose** (`v2.20.0`). Adds the `ec2-user` to the Docker group.
3.  **Nginx Setup:** Installs **Nginx** and configures it to reverse proxy traffic from port 80 to `http://localhost:8080`.
4.  **Application Deployment:** Copies application files and uses **Docker Compose** to build and run the web application container on the EC2 host.

---

## CI/CD Pipeline (GitHub Actions)

The workflow defined in `deploy.yml` runs on push/pull request to the `main` branch.

### Key Steps

* **Setup SSH Keys:** Private and public keys are generated from repository secrets (`SSH_PRIVATE_KEY` and `SSH_PUBLIC_KEY`) to enable Terraform Key Pair creation and Ansible SSH access.
* **Terraform Apply:** The deployment (with `-auto-approve`) only runs on a **`push`** to the **`main`** branch.
* **State Management:** The workflow uses artifacts to download and upload the `terraform.tfstate` file for state persistence.

### Required GitHub Secrets

You must configure the following secrets in your GitHub repository:

| Secret Name | Description |
| :--- | :--- |
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key ID. |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Access Key. |
| `SSH_PRIVATE_KEY` | The content of the private key file (e.g., `arinze`). |
| `SSH_PUBLIC_KEY` | The content of the public key file (e.g., `arinze.pub`). |

---

## Accessing the Application

The web server will be accessible via standard HTTP (Port 80) using the EC2 instance's public IP.

The IP address can be found in the Terraform output after a successful apply:


# Terraform Output
Outputs:
instance_public_ip = "X.X.X.X"