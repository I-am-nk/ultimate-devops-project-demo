# 🚀 Argo CD on EKS via EC2 — Installation & CI/CD Setup Guide

![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20EKS-FF9900?logo=amazon-aws&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-1.2x-326CE5?logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/Argo%20CD-GitOps-EF7B4D?logo=argo&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-Repo-181717?logo=github)
![DevOps](https://img.shields.io/badge/Practice-GitOps%20%7C%20CI%2FCD-4CAF50)

This guide installs and configures Argo CD on an AWS EC2 instance that has kubectl access to an Amazon EKS cluster. It also covers logging into the Argo CD UI and wiring up a GitHub repository for automated deployments.

---

## ✅ Prerequisites

- An EC2 instance with network access to the EKS API server.
- AWS CLI configured on EC2 with permissions to access EKS.  
  - Verify: (aws --version)
  - Configure: (aws configure)
- kubectl installed and configured to point to the EKS cluster.  
  - Update kubeconfig: (aws eks update-kubeconfig --region <aws-region> --name <eks-cluster-name>)
  - Verify access: (kubectl get nodes)
- Optional but recommended:
  - Use a dedicated Kubernetes namespace for Argo CD (argocd).
  - Ensure security groups and NACLs allow outbound access to the internet for pulling images and accessing GitHub.

---

## 1) Install Argo CD on EKS

Create the namespace and install Argo CD CRDs, deployments, and services:

- Create namespace and apply Argo CD manifests:
    ```bash
    kubectl create namespace argocd
    ```
    ```bash
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

- Check services:
    ```bash
    kubectl get svc -n argocd
    ```

- Expose the Argo CD server externally by switching the Service type to LoadBalancer:
    ```bash
    kubectl edit svc argocd-server -n argocd
    ```
    In the editor, change:
    ```yaml
    spec:
      type: ClusterIP
    ```
    to:
    ```yaml
    spec:
      type: LoadBalancer
    ```

- Confirm the external load balancer is provisioning:
    ```bash
    kubectl get svc -n argocd
    ```
    You should see an EXTERNAL-IP or hostname appear for argocd-server. This may take a few minutes.

- Open the Argo CD UI in the browser using the Load Balancer DNS name/hostname.

Tip:
- If you prefer port-forwarding instead of a LoadBalancer, you can use:
    ```bash
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```
    Then access: https://localhost:8080

---

## 2) Retrieve Argo CD Admin Credentials and Login

- List secrets to find the initial admin secret:
    ```bash
    kubectl get secrets -n argocd
    ```
    Look for: `argocd-initial-admin-secret`

- View the secret to get the base64-encoded password:
    ```bash
    kubectl edit secret argocd-initial-admin-secret -n argocd
    ```
    Find the key: `data.password`

- Decode the password value (replace `<base64>` with the copied value):
    ```bash
    echo "<base64>" | base64 --decode
    ```

- Login to the Argo CD UI:
  - Username: `admin`
  - Password: the decoded value
  - Access via the Load Balancer URL from earlier

Optional CLI login (if using port-forwarding or have direct access):
    ```bash
    argocd login <ARGOCD_SERVER_DNS_OR_IP> --username admin --password <decoded-password> --insecure
    ```

Security note:
- Immediately rotate the admin password or create RBAC users once logged in.

---

## 3) Create an Argo CD Application (UI Workflow)

In the Argo CD UI, click “Create Application” and fill in:

- General
  - Application Name: (e.g., product-catalog)
  - Project: Default
  - Sync Policy: Automatic
  - Enable “Self Heal” (so Argo CD reverts drift automatically)

- Source
  - Repository URL: `<your Git repo URL>`
  - Revision: `HEAD`
  - Path: `Kubernetes/productcatalog`

- Destination
  - Cluster URL: `https://kubernetes.default.svc`
  - Namespace: `(default)` or your target namespace

- Click Create

Argo CD will sync the application and deploy the manifests to your EKS cluster.

---

## 4) GitHub CI/CD: Build, Push, and Update Manifests (Context)

Typical GitHub Actions stages that feed Argo CD:

- Build
  - Compile and test the application
- Code Quality
  - Run linters and static analysis
- Docker
  - Build and push the container image to a registry (e.g., Docker Hub or ECR)
- Update Kubernetes Manifests
  - Bump the image tag in your Deployment YAML and push to the repo
- Argo CD
  - Automatically detects repo changes and syncs to EKS

---

## 5) Useful kubectl Commands

- Check Argo CD components:
    ```bash
    kubectl get pods -n argocd
    ```

- Inspect the Argo CD server service:
    ```bash
    kubectl get svc argocd-server -n argocd
    ```

- Troubleshoot (view logs of a component):
    ```bash
    kubectl logs deploy/argocd-server -n argocd
    ```
    ```bash
    kubectl logs deploy/argocd-repo-server -n argocd
    ```
    ```bash
    kubectl logs deploy/argocd-application-controller -n argocd
    ```

---

## 6) Hardening & Best Practices

- Restrict Argo CD access:
  - Use security groups and NACLs to limit public exposure of the LoadBalancer.
  - Consider private/internal load balancers or Ingress with proper authentication.
- TLS/Certificates:
  - Terminate TLS at the Load Balancer or configure certs in Argo CD.
- RBAC:
  - Create users/SSO via Dex/OIDC for team access and remove reliance on the admin account.
- Repos & Credentials:
  - Add Git repositories via HTTPS/SSH with read-only scopes when possible.
- Namespaces:
  - Prefer a dedicated namespace per app/team for isolation.

---

## 7) Clean Up

- Uninstall Argo CD:
    ```bash
    kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
    ```bash
    kubectl delete namespace argocd
    ```

- Remove the Load Balancer service (if created manually):
    ```bash
    kubectl delete svc argocd-server -n argocd
    ```

---

## 📎 Quick Reference (Copy-Friendly)

- Create namespace:
    ```bash
    kubectl create namespace argocd
    ```

- Install Argo CD:
    ```bash
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

- Get services:
    ```bash
    kubectl get svc -n argocd
    ```

- Edit server service:
    ```bash
    kubectl edit svc argocd-server -n argocd
    ```

- Get initial secret:
    ```bash
    kubectl get secrets -n argocd
    ```

- View secret:
    ```bash
    kubectl edit secret argocd-initial-admin-secret -n argocd
    ```

- Decode password:
    ```bash
    echo "<base64>" | base64 --decode
    ```

---
