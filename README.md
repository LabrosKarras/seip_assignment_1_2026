# Echo API — DevOps Assignment

A containerized Node.js Express API deployed on a local Kubernetes cluster using Minikube, with automated CI/CD via GitHub Actions.

---

## Prerequisites

Make sure you have the following installed:
- [Docker Desktop](https://docs.docker.com/desktop/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Git](https://git-scm.com/)

---

## Setup & Deployment

### 1. Clone the repository
```bash
git clone https://github.com/LabrosKarras/seip_assignment_1_2026.git
cd seip_assignment_1_2026
```

### 2. Start Minikube
```bash
minikube start --driver=docker
```

### 3. Apply all Kubernetes manifests
```bash
kubectl apply -f k8s/
```

This applies all 4 manifests in the `k8s/` directory:
- `configmap.yaml` — non-sensitive environment config
- `secret.yaml` — Base64 encoded API secret key
- `deployment.yaml` — 3 replicas of the containerized app
- `service.yaml` — ClusterIP service exposing port 80

### 4. Verify everything is running
```bash
kubectl get all -n default
kubectl get configmap,secret
```

Wait until all 3 pods show `Running` status.

### 5. Port forward to access the app
```bash
kubectl port-forward service/echo-api-service 8080:80
```

---

## Endpoints

| Endpoint | Description |
| :--- | :--- |
| `GET /` | Returns the custom welcome message from ConfigMap |
| `GET /secure-config` | Returns authorization status and masked secret suffix |
| `GET /health` | Returns cluster health status |

---

## CI/CD Pipeline

Every push to `main` automatically:
1. Checks out the repository code
2. Authenticates with GitHub Container Registry (GHCR)
3. Builds the Docker image from the `Dockerfile`
4. Pushes the image to `ghcr.io/labroskarras/echo-api:latest`