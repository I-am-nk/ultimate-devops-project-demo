
# 🚀 CI/CD Workflow for Product Catalog Service  

This repository contains a **GitHub Actions workflow** only for one micro service that is **Product Catalog** that builds, tests, performs code-quality checks, builds Docker images, and updates Kubernetes manifests for the **Product Catalog microservice**.  

## 📌 Workflow Overview  

The workflow runs automatically on **pull requests to the `main` branch**.  

It includes four jobs:  

1. **Build & Unit Tests**  
   - Sets up Go 1.22  
   - Builds the Product Catalog Service (`main.go`)  
   - Runs unit tests  

2. **Code Quality**  
   - Uses `golangci-lint` to enforce coding standards and detect issues  

3. **Docker Build & Push**  
   - Builds a Docker image for the Product Catalog service  
   - Pushes it to Docker Hub with a unique `run_id` tag  

4. **Kubernetes Manifest Update**  
   - Updates the image tag in the Kubernetes `deploy.yaml` file  
   - Commits and pushes the updated manifest back to the `main` branch  

---

## ⚙️ Setup Instructions  

### 1. Configure GitHub Secrets  
You need to add the following secrets in your GitHub repository:  

| Secret Name         | Description |
|----------------------|-------------|
| `DOCKER_USERNAME`    | Your Docker Hub username |
| `DOCKER_TOKEN`       | Docker Hub Access Token (not password) |
| `GITHUB_TOKEN`       | Provided by GitHub automatically (no setup needed) |

➡️ Go to: **Repo Settings > Secrets and Variables > Actions > New Repository Secret**  

<img width="1138" height="433" alt="image" src="https://github.com/user-attachments/assets/98b2b079-d672-4dad-939a-e45ca9941e46" />

---

### 2. Project Structure (Relevant Folders)  

```
src/product-catalog/       # Source code for Product Catalog service
    ├── main.go
    ├── go.mod
    ├── Dockerfile
kubernetes/productcatalog/ # Kubernetes deployment manifests
    ├── deploy.yaml
.github/workflows/         # CI/CD workflows
    ├── product-catalog-ci.yml
```

---

### 3. How It Works  

1. Developer raises a **Pull Request to `main`**  
2. GitHub Actions workflow triggers  
3. Workflow performs:  
   - ✅ Build & Unit Tests  
   - ✅ Code Quality Check  
   - ✅ Docker Build & Push → `dockerhub_username/product-catalog:<run_id>`  
   - ✅ Update Kubernetes manifest → auto-commits new image tag  

---

### 4. Example Image Tagging  

Each image pushed to Docker Hub is tagged with the **GitHub Run ID**, ensuring a **unique version** for every pipeline run:  

```
dockerhub_username/product-catalog:1234567890
```

---

### 5. Kubernetes Deployment  

The workflow automatically updates:  

```yaml
# kubernetes/productcatalog/deploy.yaml
image: dockerhub_username/product-catalog:<run_id>
```  

so your Kubernetes cluster always points to the latest built image.  

---

## 🎯 Benefits  

- Automated CI/CD for Go microservice  
- Enforces **best DevOps practices** (build → test → lint → containerize → deploy)  
- GitOps-style auto update for Kubernetes manifests  
- Eliminates manual image tagging and deployment steps  

---

✅ With this setup, every PR ensures **production-ready builds**, and your Kubernetes cluster always runs the **latest tested image**.  
