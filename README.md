# End-to-End DevOps Project on AWS with CI/CD Pipeline

This project demonstrates a comprehensive **end-to-end CI/CD infrastructure** for a containerized application.  
It leverages **Terraform** for AWS infrastructure provisioning, **GitHub Actions** for continuous integration, and **ArgoCD** for GitOps-based continuous deployment on a Kubernetes cluster.

---
[Project overview](https://github.com/I-am-nk/ultimate-devops-project-demo/blob/main/Project%20Overview.md)

---

## Project Architecture

![new1](https://github.com/user-attachments/assets/23c24930-9523-4a61-aea1-e01eae6553ef)

---
### 1. ☁️ AWS Infrastructure (Terraform Provisioned)
- **EC2 Instance**: Required for accessing EKS and AWS CLI.  
- **Backend for Terraform (S3 & DynamoDB)**:  
  Stores state files in S3 and uses DynamoDB for state locking to ensure collaboration.  
- **Amazon EKS (Elastic Kubernetes Service)**:  
  Fully managed Kubernetes cluster with auto-healing and auto-scaling features. Used to deploy our applications with continuous deployment support.  
- **Amazon VPC (Virtual Private Cloud)**:  
  Provides secure VPC with public/private subnets, route tables, and isolated networking for security.  
- **Amazon Route53**:  
  Used for DNS and routing traffic to our domain (e.g., [https://iamnkdevopseng.shop](https://iamnkdevopseng.shop)).  
---
### 2. 💻 Kubernetes Environment
- **EKS Cluster**: Managed Kubernetes service for deploying and managing workloads.  
- **Ingress Resource**: Exposes the application publicly.  
---
### 3. ⚙️ GitHub Actions (CI/CD Integration)
- Automates the build, test, Docker image creation, and manifest update process.  
- Pipeline Stages:  
  1. **Build** → Checkout code, setup Go, install dependencies, build services, and run unit tests.  
  2. **Code Quality** → Runs `golangci-lint` for linting and code quality checks.  
  3. **Docker** → Builds and pushes Docker images to Docker Hub.  
  4. **Update Manifests** → Updates Kubernetes manifests with new image tags and pushes them to the repo.  
---
### 4. 🔁 ArgoCD (Continuous Deployment)
- Continuously syncs Kubernetes manifests from GitHub.  
- Ensures that the application is always deployed with the latest version on EKS.  

---

## ✅ Prerequisites
- AWS account with required IAM permissions  
- Docker, AWS CLI, Terraform & Git installed locally  
- EKS Cluster (provisioned via Terraform)  

---

## 📁 Project Structure
```
.
├── .github/               # GitHub Actions & CI configs
├── ArgoCD/                # ArgoCD set up documentation  
├── internal/              # Internal tools/scripts
├── kubernetes/            # Kubernetes manifests for all services
├── pb/                    # Protocol buffers
├── src/                   # Source code of all services
├── test/                  # Testing configs
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── Makefile
├── README.md
├── docker-compose.yml
├── buildkitd.toml
├── package.json
└── other configs (.env, renovate.json5, etc.)
```

---

## 🧱 Project Components

### 🚀 Terraform (Infrastructure as Code)
Automates provisioning of:  
- VPC, subnets, internet gateway  
- EKS Cluster  
- S3 bucket & DynamoDB for state management

📄 [Terraform README](https://github.com/I-am-nk/ultimate-devops-project-terraform/blob/main/README.md)

---
### 🐳 Docker Compose
- Runs the project locally with a single `docker-compose.yml` file.  
- Helps test the application before deploying.

📄 [Docker Compose README](https://github.com/I-am-nk/ultimate-devops-project-demo/blob/main/local-setup-readme.md)

---
### ☸️ Kubernetes (Container Orchestration)
- Deployments, Services, Ingress, and LoadBalancer services.  
- Service Account setup.  
- Manifests are automatically updated by GitHub Actions.

📄 [Kubernetes README →](./kubernetes/README.md)
 
  
  ---
### 🚀 ArgoCD (GitOps Continuous Deployment)
- Auto-syncs Kubernetes manifests from GitHub.  
- Deploys the app to the EKS cluster continuously.  

📄 [ArgoCD README →](./ArgoCD/README.md)

---
### 🛠️ GitHub Actions (CI/CD)
Defines the pipeline with:  
- Code checkout  
- Build & push Docker images  
- Code quality checks  
- Kubernetes manifest updates

📄 [Github Actions README →](https://github.com/I-am-nk/ultimate-devops-project-demo/blob/main/GitHub%20Actions%20Readme.md))

---

## 👨‍💻 Author
**Nandkishor Khandare**  
Cloud & DevOps / SRE Engineer  

## 📬 **Contact**: 
[LinkedIn](https://www.linkedin.com/in/nandkishor-khandare-616492215/) | [Email](nandkishor.k6e@gmail.com)
