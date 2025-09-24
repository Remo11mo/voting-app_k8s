# Voting App on Kubernetes

This repository deploys the **Voting App** on a Kubernetes cluster.  
It uses Namespace isolation, Deployments, Services, health probes, and a NetworkPolicy to control communication.

---

## Project Overview

The Voting App is a microservices-based application that allows users to vote between two options and see live results.

It consists of the following components:

- **Vote App**: A frontend service where users cast their votes.  
- **Result App**: A frontend service that displays voting results.  
- **Worker**: A background service that transfers votes from Redis to Postgres.  
- **Redis**: An in-memory datastore for temporary vote storage.  
- **Postgres**: A relational database for persistent vote storage.  

### Data Flow

1. User casts a vote via the **Vote App**.  
2. The **Vote App** sends the vote to **Redis**.  
3. The **Worker** reads votes from **Redis** and writes them to **Postgres**.  
4. The **Result App** retrieves voting results from **Postgres** and displays them to users.  

---

## Repository Layout

```
voting-app/
│namespace.yml
├── vote-deployment.yml
├── result-deployment.yml
├── worker-deployment.yml
├── redis-deployment.yml
└── db-deployment.yml
├── vote-service.yml
├── result-service.yml
├── redis-service.yml
└── db-service.yml
├── network-policy.yml
└── README.md

---

## Prerequisites

- A running Kubernetes cluster (v1.20+ recommended)  
- `kubectl` installed and configured  
- Container runtime (containerd, Docker, etc.)  
- Sufficient node resources (CPU, Memory, and Disk space)  

---

## Deployment Instructions

1. **Clone the repository**:
   ```bash
   git clone https://github.com/<your-repo>/voting-app-k8s.git
   cd voting-app-k8s
   ```

2. **Create the namespace**:
   ```bash
   kubectl apply -f namespace.yaml
   ```

3. **Deploy all application components**:
   ```bash
   kubectl apply -f db-deployment.yaml
   kubectl apply -f redis-deployment.yaml
   kubectl apply -f vote-deployment.yaml
   kubectl apply -f result-deployment.yaml
   kubectl apply -f worker-deployment.yaml
   ```

4. **Apply the NetworkPolicy** (optional but recommended):
   ```bash
   kubectl apply -f networkpolicy.yaml
   ```

5. **Check deployed resources**:
   ```bash
   kubectl get all -n voting-app
   ```

---

## Accessing the Application

- **Vote App**:  
  Exposed via NodePort (default **30001**)  
  Access it using:  
  ```
  http://<NodeIP>:30001
  ```

- **Result App**:  
  Exposed via NodePort (default **30002**)  
  Access it using:  
  ```
  http://<NodeIP>:30002
  ```

## Cleanup

To remove all resources related to this application:

```bash
kubectl delete all --all -n voting-app
kubectl delete namespace voting-app
```
