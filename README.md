
# Automated Infrastructure Setup & Kubernetes Deployment

This repository contains a complete solution for automating, securing, and deploying a simple web application using **Linux Ubuntu**, **Ansible**, **Docker**, **Kubernetes + Helm**, **Jenkins**, and **Secrets Management**.  
It is structured with best practices, demonstrating strong command of DevOps tooling and modern CI/CD practices.

---

## Repository Structure

```
automated-infra-setup-kubernetes-main/
├── infra/
│   ├── roles/
│   │   ├── common/                # System updates, UFW, swap disable
│   │   ├── docker/                # Docker install + configuration
│   │   ├── jenkins/               # Jenkins installation & setup
│   │   ├── kubernetes/            # kubeadm, kubelet, kubectl install
│   │   ├── kubernetes-config/     # Cluster initialization configs
│   ├── inventory.ini
|   ├── ansible.cfg 
│   ├── site.yaml                  # Main Ansible playbook
│
├── app/
│   ├── Dockerfile                 # Multi‑stage optimized build
│   ├── .dockerignore
│   ├── server.js                  # Sample lightweight Node.js app
│
├── app-helm/
│   ├── Chart.yaml
│   ├── values.yaml
│   ├── templates/                 # Deployment, Service, Ingress, HPA, ConfigMap
│       ├── configmap.yaml         # contains config for the port and the env like development,staging,production mode of the app
|       ├── deployment.yaml        # replica set and container image
|       ├── hpa.yaml               # horizontal pod autoscaling from 2 to 5
|       ├── ingress.yaml           # ingress rule for / and /health path
|       ├── service.yaml           # clusterIP service for the pod
├── JenkinsPipeline                # Jenkinsfile for CI/CD pipeline
└── README.md
```

---

# Part 1 — Linux & Ansible Setup

The Ansible playbook automates provisioning of a headless **Ubuntu** environment and prepares it for container orchestration and CI/CD.

### Features

- Full system update & package installation  
- Installs:
  - Docker Engine
  - kubectl, kubeadm, kubelet
  - Helm
  - Jenkins
- Creates a **non‑root DevOps user**:
  - SSH authorized key
  - Passwordless sudo
- Secures server using **UFW**
- Disables **swap** (required for Kubernetes)
- Deploys a **test NGINX container** to verify Docker setup

### Run Ansible Playbook

```
ansible-playbook site.yaml
```

---

# Part 2 — Docker Containerization

A lightweight web application Node.js is containerized using a **multi‑stage Docker build**.

### Docker Features

- Multi‑stage build for minimal final image
- `/health` endpoint for Kubernetes readiness
- `.dockerignore` for optimized context
- Production‑ready container execution

### Build & Run Locally

```
docker build -t sample-app .
docker run -p 3000:3000 sample-app
curl localhost:3000/health
```
### After Successful Image Test, Push the docker Image to Docker Hub using docker Hub login credentials in K8 secrets and tag is created based on the Git commit SHA
### Use below command to set up dockerhub Creds
```
kubectl create secret generic dockerhub-creds --from-literal=username=15121993 --from-literal=password=xyz --namespace default
```
### Assign username and password variable in the pipeline using below command
```
DOCKER_USERNAME = sh(script: "kubectl get secret dockerhub-creds -o jsonpath='{.data.username}' | base64 --decode", returnStdout: true).trim()
DOCKER_PASSWORD = sh(script: "kubectl get secret dockerhub-creds -o jsonpath='{.data.password}' | base64 --decode", returnStdout: true).trim()
```
---

# Part 3 — Kubernetes + Helm Deployment

Deployment is fully templatized using **Helm**.  
A single‑node Kubernetes cluster is created using `kubeadm`.

### Kubernetes Components

- **Deployment** (2 replicas)
- **Service** (ClusterIP)
- **Ingress** (Nginx Ingress Controller recommended)
- **ConfigMap** (application environment variables)
- **Horizontal Pod Autoscaler (HPA)**  
  - Min replicas: 2  
  - Max replicas: 5  

### Deploy App Using Helm

```
helm install demo-app app-helm/
```

### Update Deployment

```
helm upgrade demo-app app-helm/
```

---

# Part 4 — Jenkins CI/CD Pipeline

A fully automated CI/CD pipeline is included in `JenkinsPipeline`.

### Pipeline Stages

1. **Checkout**  
   Pulls repository source.

2. **Build Docker Image**

```
docker build -t $IMAGE_NAME .
```

3. **Health Test**

```
curl http://localhost:8080/health
```

4. **Push to DockerHub**

```
docker push $IMAGE_NAME
```

5. **Helm Deploy to Kubernetes**

```
helm upgrade --install demo-app app-helm/ --set image.tag=$BUILD_NUMBER
```

### Jenkins Integrations

- Jenkins credentials store → DockerHub
- Kubernetes CLI installed via Ansible

---

# End-to-End Flow

1. Provision Ubuntu VM using Ansible  
2. Docker + Kubernetes + Jenkins installed  
3. Jenkins builds image → runs healthcheck → pushes to DockerHub  
4. Jenkins deploys application via Helm  
5. PODs scales with HPA  
6. Secrets managed securely (K8s Secrets)

---
