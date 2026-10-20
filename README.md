# Spring Boot CI/CD Demo

A small Spring Boot REST API that is built, containerized, and deployed automatically using a CI/CD pipeline and GitOps.

## What the app does
- `/` shows a simple homepage
- `/api/health` returns `{"status":"UP"}`

## Tools used
- Spring Boot 2.7 (Java 17) with Maven
- Docker and Docker Hub
- GitHub Actions (CI/CD)
- Kubernetes (Minikube)
- ArgoCD (GitOps)

## How it works
1. I push code to the `main` branch.
2. GitHub Actions builds the app, runs the test, builds the Docker image, and pushes it to Docker Hub.
3. GitHub Actions then updates the image tag in `k8s/deployment.yaml`.
4. ArgoCD notices the change in Git and automatically updates the app running in Kubernetes.

## Project structure
- `src/` - Spring Boot source code and test
- `pom.xml` - Maven dependencies
- `Dockerfile` - builds the Docker image (multi-stage)
- `.dockerignore` - files Docker ignores
- `k8s/` - Kubernetes deployment (2 replicas) and service
- `.github/workflows/ci.yml` - GitHub Actions pipeline
- `argocd/application.yaml` - ArgoCD application with auto-sync
- `screenshots/` - screenshots of each step

## Run locally
```
mvn spring-boot:run
```
Open http://localhost:8080 and http://localhost:8080/api/health

## Build the Docker image
```
docker build -t purnalti123/springboot-cicd-demo:v1 .
docker push purnalti123/springboot-cicd-demo:v1
```

## Pipeline setup
Add these two secrets in the GitHub repo (Settings > Secrets and variables > Actions):
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

No passwords are stored in the code.

## Deploy to Kubernetes
```
minikube start
kubectl apply -f k8s/
minikube service demo-app-service --url
```

## Deploy with ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd --server-side -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f argocd/application.yaml
```
ArgoCD watches the `k8s/` folder of this repo and syncs changes automatically.
