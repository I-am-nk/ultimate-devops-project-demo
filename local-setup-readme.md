# 🚀 Run Project Locally: Open Telemetry (Ultimate DevOps Project)

This guide walks you through setting up and running the Ultimate DevOps Project locally using Docker Compose on an AWS EC2 instance.

---

## 🔧 Prerequisites

### 1. Create an IAM User  
- Set up an IAM user with necessary EC2 and EBS permissions.

### 2. Launch Ubuntu EC2 Instance  
- Instance type: `t2.large`  
- Minimum volume size: **8 GB**

---

## ⚙️ Environment Setup on EC2

### 3. Install Docker  
Follow the official Docker documentation for Ubuntu:  
🔗 [Install Docker on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

### 4. Install kubectl  
🔗 [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

### 5. Install Terraform  
🔗 [Install Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

---

## 📦 Clone and Run the Project

### 6. Clone the Repository
```bash
git clone https://github.com/I-am-nk/ultimate-devops-project-demo.git
cd ultimate-devops-project-demo
```

### 7. Run with Docker Compose
```bash
docker compose up -d
```

---

## 🧱 Handling Low Storage on EC2

You may encounter storage issues due to the multi-service architecture. To resolve this:

### 8. Check Current Storage
```bash
df -h
```

### 9. Expand EC2 Volume

**Via AWS Console:**
- Go to EC2 Dashboard → Elastic Block Store → Volumes
- Find your EC2's attached volume
- Click **Actions → Modify Volume**
- Update the size to **30 GB**
- Confirm and apply changes

### 10. Extend Filesystem on EC2
```bash
# Check partitions
lsblk

# Expand partition (e.g., /dev/xvda1)
sudo growpart /dev/xvda 1

# Resize filesystem
sudo resize2fs /dev/xvda1

# Verify new space
df -h
```

---

## 🔁 Rerun Docker Compose
```bash
docker compose up -d
```

---

## 🌐 Access the Application

Open your browser and visit:  
```
http://<EC2-PUBLIC-IP>:8080
```

---

## ✅ You're all set!

Enjoy running your full-fledged DevOps monitoring stack using Docker Compose, EC2, and OpenTelemetry!
