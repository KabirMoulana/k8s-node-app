# k8s-node-app 🚀

A production-ready **Node.js / Express** application with full **Kubernetes** deployment, autoscaling, and a **GitHub Actions** CI/CD pipeline.

---

## 📁 Project Structure

```
k8s-node-app/
├── app/
│   ├── index.js              # Express app
│   ├── index.test.js         # Jest tests
│   ├── package.json
│   └── Dockerfile            # Multi-stage Docker build
├── k8s/
│   ├── namespace.yaml        # Kubernetes namespace
│   ├── configmap.yaml        # App configuration
│   ├── deployment.yaml       # Deployment (3 replicas, rolling update)
│   ├── service.yaml          # ClusterIP service
│   ├── ingress.yaml          # NGINX ingress
│   └── hpa.yaml              # Horizontal Pod Autoscaler (2–10 pods)
└── .github/
    └── workflows/
        └── ci-cd.yml         # GitHub Actions: test → build → deploy
```

---

## ⚙️ CI/CD Pipeline

```
Push to main
     │
     ▼
┌──────────┐     ┌───────────────────┐     ┌──────────────────────┐
│  Test     │────▶│ Build & Push      │────▶│ Deploy to Kubernetes │
│ (Jest)    │     │ (GHCR Docker img) │     │ (kubectl apply)      │
└──────────┘     └───────────────────┘     └──────────────────────┘
```

The pipeline:
1. **Test** — runs Jest unit tests on every push/PR
2. **Build** — builds a multi-stage Docker image and pushes to GitHub Container Registry (`ghcr.io`)
3. **Deploy** — applies all Kubernetes manifests and rolls out the new image

---

## 🚀 Getting Started

### 1. Clone & set up

```bash
git clone https://github.com/YOUR_USERNAME/k8s-node-app.git
cd k8s-node-app/app
npm install
npm test
npm start
```

App runs at `http://localhost:3000`

### 2. Run with Docker

```bash
cd app
docker build -t k8s-node-app .
docker run -p 3000:3000 k8s-node-app
```

### 3. Deploy to Kubernetes (manual)

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
kubectl apply -f k8s/hpa.yaml
```

---

## 🔧 GitHub Actions Setup

Add these **repository secrets** under `Settings → Secrets and variables → Actions`:

| Secret       | Description                                              |
|--------------|----------------------------------------------------------|
| `KUBECONFIG` | Base64-encoded kubeconfig: `cat ~/.kube/config \| base64` |

### Update the image name

In `k8s/deployment.yaml`, replace:
```yaml
image: ghcr.io/YOUR_GITHUB_USERNAME/k8s-node-app:latest
```
with your actual GitHub username.

---

## 📡 API Endpoints

| Endpoint  | Description                          |
|-----------|--------------------------------------|
| `GET /`   | App info (version, hostname, env)    |
| `GET /health` | Liveness probe — `{ status: "ok" }` |
| `GET /ready`  | Readiness probe — `{ status: "ready" }` |

---

## 🔁 Autoscaling

The HPA automatically scales pods between **2 and 10** replicas based on:
- CPU utilization > 70%
- Memory utilization > 80%

---

## 🛡️ Security Highlights

- Multi-stage Docker build (minimal final image)
- Non-root user inside the container
- Resource requests and limits on all pods
- Secrets managed via GitHub Actions secrets (never hardcoded)
