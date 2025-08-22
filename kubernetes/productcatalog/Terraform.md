Perfect 👍 Here’s your final **`README.md`** file with emojis, badges, and all steps cleaned up but untouched:

````markdown
# 🚀 Creating EKS, VPC & Remote Backend Resources on AWS using Terraform  

![Terraform](https://img.shields.io/badge/Terraform-844FBA?style=for-the-badge&logo=terraform&logoColor=white)  
![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)  
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)  
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)  

---

## 📖 Overview  
This repository contains **Infrastructure as Code (IaC)** using Terraform to deploy the **Ultimate DevOps Project Demo**.  
It provisions **VPC, EKS cluster, S3, and DynamoDB** on AWS.  

---

## ✅ What’s Included  
- **🗂️ S3 (Storing state file):** Stores the Terraform state file remotely.  
- **🔒 DynamoDB (State locking):** Prevents simultaneous operations by enabling state locking.  
- **☸️ EKS:** Creates Amazon EKS cluster for secure Kubernetes workloads.  
- **🌐 VPC:** Creates custom VPC with public subnets, route tables, and networking resources.  

---

## 📂 Project Structure  
```bash
Terraform/
│── main.tf              # Root module: entry point for Terraform execution
│── variables.tf         # Input variable definitions for the root module
│── outputs.tf           # Output values from the root module
│
├── backend/             # Backend configuration & state management
│   ├── main.tf          # Defines backend (S3 for state storage, DynamoDB for locking)
│   ├── outputs.tf       # Backend-related outputs
│   ├── terraform.tfstate        # Current Terraform state file
│   ├── terraform.tfstate.backup # Backup of the state file
│
├── modules/             # Reusable infrastructure modules
│   │
│   ├── eks/             # Amazon EKS (Kubernetes) module
│   │   ├── main.tf      # Resources for EKS cluster (nodes, roles, etc.)
│   │   ├── variables.tf # Inputs required for EKS
│   │   ├── outputs.tf   # Outputs from EKS (cluster endpoint, kubeconfig)
│   │
│   └── vpc/             # Amazon VPC networking module
│       ├── main.tf      # Resources for VPC (subnets, route tables, IGW, NAT, etc.)
│       ├── variables.tf # Inputs required for VPC
│       ├── outputs.tf   # Outputs from VPC (VPC ID, subnet IDs)
````

---

## ⚡ Deployment Steps

### 1️⃣ AWS CLI Setup

1. Configure the **AWS CLI** and provide credentials so Terraform can access AWS APIs.

2. Install AWS CLI (refer [official documentation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions))

3. Install unzip (required for CLI installation):

   ```bash
   sudo apt install unzip
   ```

4. Verify AWS CLI installation:

   ```bash
   aws --version
   ```

5. Configure AWS credentials:

   ```bash
   aws configure
   ```

   Provide:

   * AWS Access Key ID
   * AWS Secret Access Key
   * Default region
   * Default output format

6. Verify setup ✅ + *(screenshot here)*

---

### 2️⃣ Backend Setup (S3 + DynamoDB)

1. Clone the Terraform repository:

   ```bash
   git clone <repo-url>
   ```

2. Navigate to **backend folder**:

   ```bash
   cd backend
   ```

3. Initialize Terraform:

   ```bash
   terraform init
   ```

4. Validate plan:

   ```bash
   terraform plan
   ```

5. Apply configuration:

   ```bash
   terraform apply
   ```

   Confirm with **yes** when prompted.

6. Verify S3 bucket creation ✅ + *(screenshot here)*

7. Verify DynamoDB table creation ✅ + *(screenshot here)*

---

### 3️⃣ EKS & VPC Deployment

1. Navigate back to **Terraform main folder**:

   ```bash
   cd ../
   ```
2. Run Terraform commands:

   ```bash
   terraform init
   terraform plan
   terraform apply
   ```
3. Confirm with **yes** when prompted.
4. Verify created resources:

   * **EKS Cluster** ✅ + *(screenshot here)*
   * **VPC** ✅ + *(screenshot here)*

---

## 🧹 Cleanup

To destroy all infrastructure:

```bash
terraform destroy
```

---

## 🎯 Final Notes

This repository provides a **modular, production-ready Terraform setup** for:

* Remote backend with **S3 + DynamoDB**
* Kubernetes cluster with **EKS**
* Secure networking using **VPC**

---

✨ Happy Terraforming! 🚀

```

---

