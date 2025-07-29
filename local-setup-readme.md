```markdown
# Ultimate DevOps Project: OpenTelemetry Setup

This guide walks you through setting up and running the **Ultimate DevOps Project** with OpenTelemetry locally on an AWS EC2 instance. You will use Docker, Docker Compose, kubectl, and Terraform.

---

## Prerequisites

- AWS account (with console access)
- Familiarity with Linux & basic command line
- Recommended: Ubuntu EC2 (t2.large, minimum 8GB storage)

---

## Steps to Run the Project Locally

### 1. Create an IAM User
- In the AWS Console, create a new IAM user.
- Assign permissions required for EC2 and EBS management.

### 2. Launch an Ubuntu EC2 Instance
- Create an EC2 instance:
  - Type: `t2.large` (2 vCPUs, minimum 8GB RAM)
  - 8GB EBS storage (minimum)

### 3. Install Docker
- SSH into your EC2 instance.
- Install Docker following the [official documentation](https://docs.docker.com/engine/install/ubuntu/).

### 4. Install kubectl
- Install `kubectl` using the [official K8s docs](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

### 5. Install Terraform
- Install Terraform using the [official guide](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

### 6. Clone the Repository
```
git clone https://github.com/I-am-nk/ultimate-devops-project-demo.git
cd ultimate-devops-project-demo
```

### 7. Run the Project Using Docker Compose
```
docker compose up
```

### 8. Check Storage
- The multi-service project may require **more than 8GB** disk space.
- Check your available storage:
  ```
  df -h
  ```

### 9. Increase EC2 Volume Size (If Needed)
- In the AWS Console:
  - Go to **EC2 > Elastic Block Store > Volumes**.
  - Find your attached volume.
  - Click **Actions > Modify Volume**.
  - Update the size (e.g., from 8GB to 30GB).
  - Click **Modify** and confirm.

### 10. Extend the Filesystem on the EC2 Instance
- After modifying the volume, SSH into your EC2 and run:
  ```
  lsblk                                # Check partitions
  sudo growpart /dev/xvda 1            # Extend the partition (if /dev/xvda1 is your root)
  sudo resize2fs /dev/xvda1            # Expand filesystem
  df -h                                # Confirm new size
  ```

### 11. Rerun Docker Compose
```
docker compose up -d
```

### 12. Access the Project
- Open your browser and go to:
  ```
  http://<Ec2-public-ip>:8080
  ```
  Replace `` with your instance's public IP address.

---

## Notes

- **Security Groups:** Make sure your EC2 security group allows inbound traffic on port **8080**.
- **Disk Space:** If you still see “no space left on device” errors, repeat steps 9–10 to allocate more storage.
- **Configuration:** For environment variables or advanced configuration, refer to the repository’s documentation or `.env` files.

---

Happy DevOps-ing! 🚀
```
