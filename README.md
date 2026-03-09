# 🚀 Jenkins CI/CD Pipeline with Docker & Kubernetes (Kind)

![CI/CD](https://img.shields.io/badge/CI/CD-Jenkins-blue)
![Docker](https://img.shields.io/badge/Container-Docker-blue)
![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes-blue)
![Kind](https://img.shields.io/badge/Kubernetes-Kind-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

# 📌 Project Overview

This project demonstrates a **complete CI/CD pipeline** using:

- **GitHub** – Source code repository  
- **Jenkins** – CI/CD automation  
- **Docker** – Containerization  
- **Kubernetes (Kind)** – Container orchestration  

When code is pushed to GitHub, Jenkins automatically:

1. Builds a Docker image
2. Loads the image into a Kubernetes cluster
3. Deploys the application using Kubernetes manifests
4. Exposes the service via NodePort

The application becomes accessible through the Kubernetes service.

---

# 🏗 Architecture

```text
Developer
   │
   │ git push
   ▼
GitHub Repository
   │
   ▼
Jenkins Pipeline
   │
   ├── Build Docker Image
   ├── Load Image into Kind Cluster
   └── Deploy to Kubernetes
   ▼
Kubernetes Cluster (Kind)
   │
   ├── Deployment
   │      └── Pod
   │            └── Nginx Container
   │
   └── Service (NodePort)
           │
           ▼
    http://VM-IP:30007
```

---

# ⚙️ Tech Stack

| Tool | Purpose |
|-----|------|
| GitHub | Source Code Management |
| Jenkins | CI/CD Automation |
| Docker | Containerization |
| Kubernetes | Container Orchestration |
| Kind | Local Kubernetes Cluster |
| Nginx | Web Server |

---

# 📦 Project Structure

```
project-root
│
├── Jenkinsfile
├── Dockerfile
├── index.html
│
├── nginx-cicd-project
│   ├── deployment.yaml
│   └── service.yaml
│
└── kind-config.yaml
```

---

# 🔧 Prerequisites

Install the following tools on your system:

- Docker
- Jenkins
- kubectl
- Kind
- Git

---

# 🐳 Step 1 — Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

Add user to docker group:

```bash
sudo usermod -aG docker $USER
```

---

# ☸️ Step 2 — Install kubectl

```bash
sudo snap install kubectl --classic
```

Verify:

```bash
kubectl version --client
```

---

# ☸️ Step 3 — Install Kind

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x kind
sudo mv kind /usr/local/bin/
```

Verify:

```bash
kind --version
```

---

# ☸️ Step 4 — Create Kubernetes Cluster

Create `kind-config.yaml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30007
    hostPort: 30007
    protocol: TCP
```

Create cluster:

```bash
kind create cluster --config kind-config.yaml
```

Verify:

```bash
kubectl get nodes
```

---

# 🚀 Step 5 — Kubernetes Deployment

`deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-website
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-website
  template:
    metadata:
      labels:
        app: nginx-website
    spec:
      containers:
      - name: nginx-website
        image: nginx-website:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 80
```

---

# 🌐 Step 6 — Kubernetes Service

`service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx-website
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

---

# 🤖 Jenkins Pipeline

The Jenkins pipeline performs the following stages:

1️⃣ Clone repository  
2️⃣ Build Docker image  
3️⃣ Load image into Kind cluster  
4️⃣ Deploy to Kubernetes  

Example commands used:

```bash
docker build -t nginx-website .
kind load docker-image nginx-website
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl rollout restart deployment nginx-website
```

---

# 🌍 Access the Application

After deployment the application will be available at:

```
http://<VM-IP>:30007
```

Example:

```
http://192.168.1.100:30007
```

---

# 🔄 Kubernetes Self-Healing Demo

Delete a running pod:

```bash
kubectl delete pod <pod-name>
```

Kubernetes automatically creates a new pod to maintain the desired state.

---

# 📊 Useful Kubernetes Commands

Check pods

```bash
kubectl get pods
```

Check deployments

```bash
kubectl get deployment
```

Check services

```bash
kubectl get svc
```

Watch pod changes

```bash
kubectl get pods -w
```

---

# 📚 Learning Outcomes

This project demonstrates:

- CI/CD pipeline creation
- Docker containerization
- Kubernetes deployments
- Kubernetes services & networking
- DevOps automation workflow

---

# 👨‍💻 Author

Vipul Singh  
DevOps & Cloud Enthusiast
