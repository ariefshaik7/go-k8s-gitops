# Go Web App CI/CD with GitHub Actions, Docker, Kubernetes & ArgoCD

This repository sets up an automated CI/CD pipeline for a **Go-based web application** using GitHub Actions. It integrates Docker for containerization and GitOps deployment using Helm and ArgoCD on Kubernetes.

##  Technology Stack

| Component         | Purpose                                  |
|-------------------|------------------------------------------|
| Go (Golang)       | Web application backend                  |
| GitHub Actions    | CI/CD workflow automation                |
| Docker            | Containerization                         |
| Helm              | Kubernetes package manager               |
| Kubernetes        | Container orchestration platform         |
| ArgoCD            | GitOps-based continuous delivery         |
| GitHub            | Source and Helm chart repository         |
| Docker Hub        | Container image registry                 |

##  CI/CD Workflow Overview

Located at `.github/workflows/ci-cd.yml`, this workflow automates the build, test, image creation, and deployment stages for the Go app.

###  Pipeline Steps

1. **Build & Test**
   - Sets up Go (1.22)
   - Builds the app binary
   - Runs unit tests

2. **Code Quality (Linting)**
   - Uses `golangci-lint` to analyze the code for best practices and issues

3. **Docker Image**
   - Builds and pushes a Docker image to Docker Hub
   - Tags image with GitHub workflow run ID

4. **Helm Chart Update**
   - Updates the `image.tag` in `helm/go-web-app-chart/values.yaml`
   - Commits and pushes the change to Git

5. **GitOps Deployment**
   - ArgoCD detects the Helm chart update and syncs the Kubernetes cluster accordingly

##  Repository Secrets

Add the following secrets under **GitHub → Settings → Secrets and variables → Actions**:

| Name              | Description                                  |
|-------------------|----------------------------------------------|
| `DOCKER_USERNAME` | Your Docker Hub username                     |
| `DOCKER_PASSWORD` | Your Docker Hub password or access token     |
| `TOKEN`           | GitHub Personal Access Token (with `repo` scope) for pushing chart updates |

##  ArgoCD Setup (Post-Helm Chart Push)

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Expose ArgoCD UI via LoadBalancer
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

##  Ingress Controller (NGINX) Setup

> If you're using **AKS** (Azure Kubernetes Service), you can install NGINX with:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.13.0/deploy/static/provider/cloud/deploy.yaml
```

 For other platforms like GKE, EKS, Minikube etc., refer to the official documentation:  
 https://kubernetes.github.io/ingress-nginx/deploy/

##  Access the App

Map the domain from your Ingress config to the controller IP in `/etc/hosts`:

```bash
<INGRESS_IP>   go-web-app.local
```

This repo automates the CI/CD of a Go web app using GitHub Actions, Docker, and GitOps deployment via Helm and ArgoCD to Kubernetes.

---