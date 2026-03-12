# AWS ECR Hands-On Labs

A collection of hands-on labs for learning **AWS Elastic Container Registry (ECR)** — from manually pushing Docker images to building fully automated CI/CD pipelines with Jenkins that deploy to ECS.

Both labs use **Terraform** to provision the required AWS infrastructure (EC2 instances, IAM roles, security groups) and work with a simple **Node.js To-Do App** as the sample containerized application.

---

## Repository Structure

```
.
├── README.md                      # This file
├── ecr-01-pushing-docker-images/
│   ├── ecr-ec2.tf                 # Terraform: Docker-enabled EC2 instance
│   └── README.md                  # Step-by-step lab guide
└── ecr-02-jenkins-pipeline-ecs/
    ├── ecr-ecs-ec2-jenkins.tf     # Terraform: Jenkins server with Docker, Git, AWS CLI
    └── README.md                  # Step-by-step lab guide
```

---

## Lab Overview

### ECR-01 — Pushing Docker Images to ECR Manually

**Goal:** Understand the fundamentals of AWS ECR by manually creating repositories, building Docker images, and pushing/pulling them via the CLI.

**What You Will Learn:**
- Provision a Docker-enabled EC2 instance with Terraform (including IAM role for ECR access)
- Create and configure ECR repositories via the AWS Console and AWS CLI
- Authenticate Docker CLI to an ECR registry
- Build, tag, push, and pull Docker images to/from ECR
- Set up ECR lifecycle policies to manage image retention
- Clean up repositories and images using both the Console and CLI

**Infrastructure (Terraform):**
- Amazon Linux 2023 EC2 instance with Docker and Docker Compose pre-installed
- IAM Role with `AmazonEC2ContainerRegistryFullAccess` policy
- Security group allowing HTTP (80) and SSH (22)

**Key AWS Services:** ECR, EC2, IAM

---

### ECR-02 — Jenkins Pipeline to Deploy Docker Images on ECR to ECS

**Goal:** Build an end-to-end CI/CD pipeline using Jenkins that automatically builds Docker images, pushes them to ECR, and deploys the application to an ECS Fargate cluster — all triggered by GitHub webhooks.

**What You Will Learn:**
- Provision a Jenkins server on EC2 with Terraform (pre-configured with Docker, Git, AWS CLI)
- Set up a GitHub repository with webhook integration for automated builds
- Create Jenkins pipelines using `Jenkinsfile` (Pipeline as Code)
- Build Docker images and push them to ECR as part of a Jenkins pipeline
- Create an ECS cluster, register task definitions, and launch Fargate services via AWS CLI
- Trigger automated deployments through code changes (push-to-deploy workflow)
- Clean up ECR repositories, ECS services, and clusters

**Infrastructure (Terraform):**
- Amazon Linux 2023 EC2 instance running Jenkins (JDK 17) with Docker and Git
- GitHub repository created automatically via the GitHub Terraform provider
- IAM Role with `AmazonEC2ContainerRegistryFullAccess`, `AmazonECS_FullAccess`, and `AdministratorAccess` policies
- Security group allowing HTTP (80), Jenkins UI (8080), and SSH (22)

**Key AWS Services:** ECR, ECS (Fargate), EC2, IAM

---

## Prerequisites

- An **AWS Account** with permissions to create EC2, ECR, ECS, and IAM resources
- **Terraform** installed locally (v1.0+)
- An **SSH key pair** registered in the target AWS region
- **Git** installed locally
- A **GitHub account** with a Personal Access Token (for ECR-02)

## Getting Started

1. **Clone this repository:**
   ```bash
   git clone https://github.com/<your-username>/aws-ecr-hands-on-labs.git
   cd aws-ecr-hands-on-labs
   ```

2. **Choose a lab** and navigate to its directory.

3. **Update the Terraform variables** — each `.tf` file contains a `locals` block where you need to set your own key pair name, username, and (for ECR-02) GitHub credentials.

4. **Provision the infrastructure:**
   ```bash
   terraform init
   terraform apply
   ```

5. **Follow the step-by-step instructions** in the lab's `README.md`.

6. **Clean up when finished** — each lab includes cleanup steps. You can also destroy Terraform-managed resources with:
   ```bash
   terraform destroy
   ```

## Architecture Diagrams

### ECR-01 — Manual Workflow

```
Developer (SSH) ──► EC2 Instance (Docker) ──► AWS ECR
                         │                        │
                    docker build              docker push
                    docker tag                docker pull
```

### ECR-02 — Automated CI/CD Pipeline

```
GitHub (push) ──► Jenkins (webhook) ──► Docker Build ──► ECR Push ──► ECS Deploy
                       │                                                  │
                  Jenkinsfile                                    Fargate Task (port 3000)
```

## Important Notes

- **Security:** The Terraform files contain placeholder credentials and key names. Never commit real tokens or secrets to a public repository. Use environment variables or a secrets manager instead.
- **Region:** Both labs default to `us-east-1`. Update the provider block in the `.tf` files if you want to use a different region.
- **Cost:** These labs create billable AWS resources (EC2 instances, ECS tasks). Make sure to run the cleanup steps and `terraform destroy` when you are done to avoid unexpected charges.
- **Ports:** The To-Do app listens on port `3000` inside the container, mapped to port `80` on the EC2 host. For ECS deployments, ensure your security group allows traffic on port `3000`.

## Technologies Used

| Category         | Tools & Services                              |
|------------------|-----------------------------------------------|
| Cloud            | AWS (ECR, ECS Fargate, EC2, IAM)              |
| IaC              | Terraform                                     |
| Containerization | Docker, Docker Compose                        |
| CI/CD            | Jenkins, GitHub Webhooks                      |
| OS               | Amazon Linux 2023                             |
| Application      | Node.js (To-Do App)                           |

## License

This project is intended for educational and training purposes.
