# 🗳️ Kubernetes Voting App

A distributed voting application deployed on Kubernetes, demonstrating microservices architecture with multiple interconnected components.

![Architecture](https://raw.githubusercontent.com/dockersamples/example-voting-app/main/architecture.excalidraw.png)

## 📋 Overview

This is the classic **Docker Example Voting App** deployed on Kubernetes. Users can vote between **CATS** 🐱 and **DOGS** 🐶, and watch the results update in real-time.

## 🏗️ Architecture

```
                    ┌─────────────┐
                    │   Browser   │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
      ┌───────────────┐         ┌───────────────┐
      │  Vote (Python)│         │Result (Node.js)│
      │   Port 31000  │         │   Port 31001   │
      └───────┬───────┘         └───────┬───────┘
              │                         │
              ▼                         │
      ┌───────────────┐                 │
      │     Redis     │                 │
      │   Port 6379   │                 │
      └───────┬───────┘                 │
              │                         │
              ▼                         │
      ┌───────────────┐                 │
      │ Worker (.NET) │                 │
      └───────┬───────┘                 │
              │                         │
              ▼                         ▼
      ┌─────────────────────────────────────┐
      │         PostgreSQL Database         │
      │             Port 5432               │
      └─────────────────────────────────────┘
```

## 🧩 Components

| Component | Technology | Description | Service Type |
|-----------|------------|-------------|--------------|
| **Vote** | Python Flask | Frontend for casting votes | NodePort (31000) |
| **Result** | Node.js Express | Frontend for viewing results | NodePort (31001) |
| **Worker** | .NET Core | Background processor | None (no incoming connections) |
| **Redis** | Redis Alpine | Message queue for votes | ClusterIP (internal) |
| **DB** | PostgreSQL 15 | Persistent vote storage | ClusterIP (internal) |

## 📁 Project Structure

```
kube/
├── vote-deployment.yaml      # Vote app pods
├── vote-service.yaml         # Vote NodePort service
├── result-deployment.yaml    # Result app pods
├── result-service.yaml       # Result NodePort service
├── worker-deployment.yaml    # Worker pods (no service needed)
├── redis-deployment.yaml     # Redis pods
├── redis-service.yaml        # Redis ClusterIP service
├── db-deployment.yaml        # PostgreSQL pods
├── db-service.yaml           # PostgreSQL ClusterIP service
└── README.md
```

## 🚀 Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed
- [Minikube](https://minikube.sigs.k8s.io/docs/start/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed

### 1. Start Minikube

```powershell
minikube start --driver=docker
```

### 2. Deploy the Application

```powershell
cd D:\kube
kubectl apply -f .
```

### 3. Verify Pods are Running

```powershell
kubectl get pods
```

Expected output:
```
NAME                      READY   STATUS    RESTARTS   AGE
db-xxx                    1/1     Running   0          1m
redis-xxx                 1/1     Running   0          1m
result-xxx                1/1     Running   0          1m
vote-xxx                  1/1     Running   0          1m
worker-xxx                1/1     Running   0          1m
```

### 4. Access the Application

```powershell
# Open Vote UI in browser
minikube service vote

# Open Result UI in browser
minikube service result
```

Or get the URLs directly:
```powershell
minikube service vote --url
minikube service result --url
```

## 🌐 Accessing the App

| App | Command | Default NodePort |
|-----|---------|------------------|
| Vote UI | `minikube service vote` | 31000 |
| Result UI | `minikube service result` | 31001 |

## 📝 Useful Commands

### Check Status

```powershell
# View all pods
kubectl get pods

# View all services
kubectl get services

# View all resources
kubectl get all

# Get Minikube IP
minikube ip
```

### Debugging

```powershell
# View logs for a pod
kubectl logs <pod-name>

# Describe a pod (detailed info)
kubectl describe pod <pod-name>

# Execute command in a pod
kubectl exec -it <pod-name> -- /bin/sh
```

### Cleanup

```powershell
# Delete all resources
kubectl delete -f .

# Stop Minikube
minikube stop

# Delete Minikube cluster
minikube delete
```

## 🔧 Configuration

### Ports

| Service | Container Port | NodePort |
|---------|---------------|----------|
| Vote | 80 | 31000 |
| Result | 80 | 31001 |
| Redis | 6379 | - |
| PostgreSQL | 5432 | - |

### Database Credentials

```yaml
POSTGRES_USER: postgres
POSTGRES_PASSWORD: postgres
```

> ⚠️ **Note**: These are default credentials for demo purposes. Use Kubernetes Secrets for production!

## 🔄 Data Flow

1. **User votes** → Vote frontend receives the choice
2. **Vote → Redis** → Vote is pushed to Redis queue
3. **Worker → Redis** → Worker pulls votes from Redis
4. **Worker → PostgreSQL** → Worker saves votes to database
5. **Result → PostgreSQL** → Result app reads from database
6. **User sees results** → Real-time updates on Result page

## 📚 Learning Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Docker Example Voting App](https://github.com/dockersamples/example-voting-app)

## 🤝 Contributing

Feel free to open issues or submit pull requests!

## 📄 License

This project is for educational purposes, based on Docker's example voting app.

---

Made with ❤️ for learning Kubernetes
