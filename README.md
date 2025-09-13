# Terraform-Ansible-Automation
# End-to-end DevOps solution that provisions infrastructure with Terraform and configures it with Ansible.

In many fast-growing companies, infrastructure provisioning and server configuration are often performed manually. This leads to inconsistent environments, security risks, and delays in deploying applications.

As a DevOps Engineer, I designed this project to demonstrate how Infrastructure as Code (IaC) with Terraform and Ansible can solve these challenges by delivering scalable, consistent, and automated deployments.

This repository shows how I used Terraform to provision infrastructure in the cloud and Ansible to configure the servers — reducing deployment time from hours to minutes and ensuring reproducibility across environments.

# Problem Statement

The engineering team was struggling with:

❌ Inconsistent server configurations (different dependencies on each instance).

❌ Manual patching leading to potential security vulnerabilities.

❌ Delays in deploying new environments for development and testing.

To fix this, I introduced a Terraform + Ansible workflow for automated provisioning and configuration.

# Solution Overview

Terraform provisions the infrastructure (VPC, subnets, EC2 instances, security groups, load balancer).

Ansible configures those instances (web servers, databases, security hardening, monitoring).

Deploy Script ties the workflow together, making it a one-command deployment pipeline.

By combining these tools, I achieved:

⚡ Faster deployments (minutes instead of hours).

🔒 Secure and consistent environments.

📦 Reusable infrastructure definitions.

🏗️ Architecture

The architecture for this setup looks like this:

        ┌───────────────────────────────┐
        │           VPC / VNet          │
        │                               │
        │   ┌───────────────┐           │
        │   │ Load Balancer │           │
        │   └───────┬───────┘           │
        │           │                   │
        │   ┌───────┴────────┐          │
        │   │    EC2/VMs     │  <--- Provisioned by Terraform
        │   └───────┬────────┘          
        │           │
        │   ┌───────┴────────┐
        │   │ Configured w/  │  <--- Configured by Ansible
        │   │ Nginx + DB +   │
        │   │ Security Rules │
        │   └────────────────┘
        │
        └───────────────────────────────┘

# Repository Structure
terraform-ansible-automation/
│
├── terraform/
│   ├── main.tf            # VPC, Subnets, EC2 Instances
│   ├── variables.tf       # Configurable variables
│   ├── outputs.tf         # Exports instance IPs
│   └── provider.tf        # AWS/Azure provider definition
│
├── ansible/
│   ├── inventory.ini      # Terraform-provisioned hosts
│   ├── playbooks/
│   │   ├── web.yml        # Setup and configure Nginx
│   │   ├── db.yml         # Setup PostgreSQL
│   │   └── hardening.yml  # Security configurations
│   └── roles/
│       ├── common/        # Basic packages, firewall rules
│       └── monitoring/    # Monitoring agent installation
│
├── scripts/
│   └── deploy.sh          # Script to run Terraform + Ansible
│
└── docs/
    └── architecture.png   # Architecture diagram

# Implementation
1. Provision Infrastructure with Terraform

Terraform code (main.tf) creates:

A VPC with public/private subnets.

A security group allowing SSH + HTTP/HTTPS.

EC2 instances for web and database servers.

Run the following:

cd terraform
terraform init
terraform apply -auto-approve


This outputs the public/private IPs of the servers.

2. Configure Servers with Ansible

After provisioning, use Ansible to:

Install Nginx as a web server.

Install and configure PostgreSQL.

Apply security hardening (firewall rules, updates).

Deploy a monitoring agent (Prometheus Node Exporter).

Example:

cd ansible
ansible-playbook -i inventory.ini playbooks/web.yml
ansible-playbook -i inventory.ini playbooks/db.yml
ansible-playbook -i inventory.ini playbooks/hardening.yml

 3. Automate Workflow with Deploy Script

For simplicity, a deploy.sh script ties everything together:

#!/bin/bash
set -e

# Provision Infrastructure
cd terraform
terraform init
terraform apply -auto-approve

# Fetch Terraform outputs
EC2_IP=$(terraform output -raw web_instance_ip)

# Run Ansible configuration
cd ../ansible
ansible-playbook -i "$EC2_IP," -u ubuntu playbooks/web.yml
ansible-playbook -i "$EC2_IP," -u ubuntu playbooks/db.yml


Run with:

./scripts/deploy.sh

# Results

Deployment time reduced from 3-4 hours to ~10 minutes.

Every server is configured consistently.

Security baselines are applied automatically.

The setup can be replicated across dev, staging, and prod with minor variable changes.

# Future Improvements

Integrate with CI/CD (GitHub Actions, GitLab CI, or Jenkins).

Add Kubernetes support for container orchestration.

Integrate HashiCorp Vault for secret management.

Expand to a multi-region deployment with load balancing.

# How to Use This Repo

Clone the repository:

git clone https://github.com/your-username/terraform-ansible-automation.git
cd terraform-ansible-automation


Update variables in terraform/variables.tf for your environment.

Update ansible/inventory.ini with your provisioned server IPs.

Run Terraform and Ansible as described above.

# Conclusion

This project highlights how Terraform and Ansible can be combined to fully automate cloud deployments. By shifting from manual provisioning to Infrastructure as Code, we gain speed, security, and repeatability.

Infrastructure is no longer a bottleneck — it’s now scalable and version-controlled, enabling faster and more reliable software delivery.
