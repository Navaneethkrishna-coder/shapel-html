# 🚀 Static Website CI/CD & GitOps Pipeline

An end-to-end DevOps project that demonstrates how a static website can be containerized, analyzed, continuously delivered, and deployed to Kubernetes using Jenkins, SonarQube, Docker, Docker Hub, and Argo CD.

> **Note:** The website itself is a static HTML/CSS/JavaScript template. The main purpose of this project is to demonstrate the DevOps pipeline and deployment workflow.

---

## 🏗️ Project Overview

This project implements a CI/CD and GitOps-oriented workflow for deploying a containerized static website to a Kubernetes cluster running on Minikube.

### Pipeline Flow

```text
Developer
    │
    ▼
 GitHub
    │
    ▼
 Jenkins
    │
    ├── Checkout Source Code
    │
    ├── SonarQube Analysis
    │
    ├── Quality Gate
    │
    ├── Build Docker Image
    │
    ├── Push Image to Docker Hub
    │
    ├── Update Kubernetes Deployment
    │
    └── Update Kubernetes Manifest
             │
             ▼
        Argo CD
             │
       Auto Sync
             │
             ▼
       Kubernetes
        (Minikube)
             │
       ┌─────┴─────┐
       ▼           ▼
   Pod 1         Pod 2
       │           │
       └─────┬─────┘
             ▼
       Static Website
```

---

## 🛠️ Technologies Used

| Technology | Purpose                                        |
| ---------- | ---------------------------------------------- |
| GitHub     | Source code and Kubernetes manifest management |
| Jenkins    | CI/CD pipeline automation                      |
| SonarQube  | Static code analysis and quality gate          |
| Docker     | Containerization                               |
| Docker Hub | Docker image registry                          |
| Kubernetes | Container orchestration                        |
| Minikube   | Local Kubernetes cluster                       |
| Argo CD    | GitOps-based continuous delivery               |
| Nginx      | Web server inside the Docker container         |

---

## 🔄 CI/CD Pipeline

The Jenkins pipeline contains the following stages:

### 1. Checkout

Jenkins checks out the application source code from the `main` branch of GitHub.

### 2. SonarQube Analysis

The source code is analyzed using SonarQube to identify code quality issues and bugs.

A SonarQube Quality Gate is used to validate the analysis result.

### 3. Build Docker Image

The application is packaged into a Docker image using the following Dockerfile:

```dockerfile
FROM nginx:alpine

COPY . /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Nginx serves the static website from inside the container.

### 4. Push Image to Docker Hub

The Docker image is pushed to Docker Hub:

```text
dockernavaneeth/shapel-website
```

### 5. Update Deployment

The Kubernetes deployment is updated with the required Docker image version.

### 6. Update Kubernetes Manifest

The Kubernetes manifest stored in the GitHub repository is updated so that Argo CD can synchronize the desired application state with the Kubernetes cluster.

---

## ☸️ Kubernetes Deployment

The application is deployed using a Kubernetes `Deployment` and `Service`.

### Deployment

The application runs with **2 replicas**:

```yaml
spec:
  replicas: 2
```

This creates two application pods.

The deployment also includes:

* CPU and memory requests
* CPU and memory limits
* HTTP liveness probe
* `imagePullPolicy: Always`

Example resource configuration:

```yaml
resources:
  limits:
    cpu: "250m"
    memory: "256Mi"
  requests:
    cpu: "100m"
    memory: "128Mi"
```

### Liveness Probe

Kubernetes checks whether the website is responding:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 10
```

### Service

A Kubernetes `ClusterIP` Service exposes the application internally within the cluster:

```yaml
type: ClusterIP
```

---

## 🔱 Argo CD

Argo CD is installed inside the Minikube Kubernetes cluster.

It monitors the Kubernetes manifests stored in Git and synchronizes the desired state with the running Kubernetes application.

The Argo CD application is configured with **automatic synchronization**.

The deployed application reaches:

* **Application Health:** Healthy
* **Sync Status:** Synced
* **Auto Sync:** Enabled

The Argo CD application tree contains:

```text
Application
    │
    ├── Service
    │
    └── Deployment
          │
          └── ReplicaSet
                │
                ├── Pod 1
                └── Pod 2
```

---

## 💻 Environment

The Kubernetes environment for this project is:

```text
Operating System: macOS
Kubernetes: Minikube
Container Runtime: Docker
GitOps: Argo CD
CI/CD: Jenkins
Code Quality: SonarQube
```

Jenkins and SonarQube are used as part of the local development/DevOps environment, with SonarQube running on an AWS EC2 instance.

---

## ▶️ Running the Project

### Prerequisites

Install/configure:

* Git
* Docker
* Jenkins
* SonarQube
* Minikube
* kubectl
* Argo CD

### Start Minikube

```bash
minikube start --driver=docker
```

Check the cluster:

```bash
kubectl get nodes
```

### Deploy Kubernetes Resources

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Check the deployment:

```bash
kubectl get deployments
```

Check the pods:

```bash
kubectl get pods
```

Check the service:

```bash
kubectl get services
```

### Access the Application

For Minikube, the service can be accessed using:

```bash
minikube service static-website-service
```

---

## 🔐 Jenkins Pipeline Trigger

The Jenkins pipeline is currently started manually using **Build Now**.

A GitHub webhook is not configured in the current implementation.

Therefore, this project demonstrates the complete pipeline stages and deployment workflow, but it should not be described as fully event-triggered CI/CD from every GitHub push.

---

## 📁 Repository Structure

```text
shapel-html/
│
├── index.html
├── about.html
├── blog.html
├── gallery.html
├── service.html
│
├── Dockerfile
├── Jenkinsfile
│
└── k8s/
    ├── deployment.yaml
    └── service.yaml
```

---

## 🎯 Key DevOps Concepts Demonstrated

This project helped demonstrate practical experience with:

* Source code management with Git/GitHub
* Jenkins pipeline creation
* Static code analysis
* SonarQube Quality Gates
* Docker image creation
* Docker image publishing
* Kubernetes Deployments
* Kubernetes Services
* Kubernetes replicas
* Kubernetes health checks
* Kubernetes resource management
* GitOps concepts
* Argo CD synchronization
* Minikube-based Kubernetes deployment
* Nginx containerization

---

## 📸 Deployment

The application can be monitored through the Argo CD dashboard, where the application health, synchronization status, Kubernetes resources, and running pods can be viewed.

---

## 🔗 Repository

GitHub:

[https://github.com/Navaneethkrishna-coder/shapel-html](https://github.com/Navaneethkrishna-coder/shapel-html)

---

## 👨‍💻 Author

**Navaneeth Krishna**

Computer Science Engineering | DevOps & Cloud Enthusiast

GitHub:

[https://github.com/Navaneethkrishna-coder](https://github.com/Navaneethkrishna-coder)

---

## 🚀 Future Improvements

Possible improvements to this project include:

* Configure a GitHub webhook to automatically trigger Jenkins
* Separate application source and Kubernetes manifests into dedicated repositories
* Add automated application testing
* Add Jenkins notifications
* Deploy to a managed Kubernetes service such as Amazon EKS
* Add Ingress and HTTPS/TLS
* Add Prometheus and Grafana monitoring
* Add vulnerability scanning for Docker images
