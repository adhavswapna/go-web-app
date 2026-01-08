# Go Web Application

This is a simple website written in Golang. It uses the `net/http` package to serve HTTP requests.

## Running the server

To run the server, execute the following command:

```bash
go run main.go
```

The server will start on port 8080. You can access it by navigating to `http://localhost:8080/courses` in your web browser.

## Looks like this

![Website](static/images/golang-website.png)


# Go Web App 🚀

A production-ready **Go web application** deployed on **Kubernetes (k3s)** using **Docker, Helm, GitHub Actions (Self-Hosted Runner), and Argo CD (GitOps)**.

This repository demonstrates a **real-world DevOps workflow**:
Code → CI (Self-Hosted Runner) → Image Build → GitOps → Argo CD Sync → Kubernetes

---

## 🧰 Tech Stack

- **Language:** Go (net/http)
- **Containerization:** Docker
- **Kubernetes:** k3s
- **Helm:** Application packaging
- **GitOps CD:** Argo CD
- **CI:** GitHub Actions (Self-Hosted Runner)
- **Ingress:** Traefik (default with k3s)
- **Linting:** golangci-lint

---

## 📂 Project Structure

```text
go-web-app/
├── cmd/                     # Application entrypoint
├── internal/                # Business logic
├── static/                  # HTML / static files
├── Dockerfile
├── go.mod
├── go.sum
├── helm/
│   └── go-web-app-chart/
│       ├── templates/
│       │   ├── deployment.yaml
│       │   ├── service.yaml
│       │   └── ingress.yaml
│       ├── values.yaml
│       └── Chart.yaml
├── .github/
│   └── workflows/
│       └── ci.yml
└── README.md


🚀 Local Development
Run locally
go run main.go


Access:

http://localhost:8080

🐳 Docker Build & Run
docker build -t go-web-app:latest .
docker run -p 8080:8080 go-web-app:latest

☸️ Kubernetes Setup (k3s)
1️⃣ Install k3s
curl -sfL https://get.k3s.io | sh -


Verify:

kubectl get nodes -o wide


k3s includes:

Traefik (Ingress)
Containerd
ServiceLB

🧑‍💻 GitHub Actions – Self-Hosted Runner
Why Self-Hosted Runner?

Faster builds

Free (no GitHub minutes)

Direct Docker access

2️⃣ Create Self-Hosted Runner

GitHub Repo → Settings → Actions → Runners → New self-hosted runner

On your server/VM:

mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/latest/download/actions-runner-linux-x64.tar.gz
tar xzf actions-runner-linux-x64.tar.gz


Configure:

./config.sh --url https://github.com/adhavswapna/go-web-app --token <RUNNER_TOKEN>


Start:

./run.sh


(Optional – background service)

sudo ./svc.sh install
sudo ./svc.sh start

🔁 CI Pipeline (GitHub Actions)

Runs on self-hosted runner

Lints Go code

Builds Docker image

Pushes image to Docker Hub / Registry

Example runner config:

runs-on: self-hosted

📦 Helm Deployment
Install app using Helm
helm install go-web-app ./helm/go-web-app-chart


Upgrade:

helm upgrade go-web-app ./helm/go-web-app-chart

🔄 Argo CD Installation (GitOps)
1️⃣ Create namespace
kubectl create namespace argocd

2️⃣ Install Argo CD
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


Wait:

kubectl get pods -n argocd

3️⃣ Access Argo CD UI (k3s / local)
kubectl port-forward svc/argocd-server -n argocd 8080:443


Open:

https://localhost:8080


Username:

admin


Password:

kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo

📘 Create Argo CD Application
argocd app create go-web-app \
  --repo https://github.com/adhavswapna/go-web-app.git \
  --path helm/go-web-app-chart \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default


Sync:

argocd app sync go-web-app


Enable auto-sync (recommended):

argocd app set go-web-app --sync-policy automated

🌐 Ingress (Traefik)

Example host:

go-web-app.local


Add to /etc/hosts:

127.0.0.1 go-web-app.local

🔐 Security Notes

Use GitHub Secrets for:

Docker registry credentials

Do not commit tokens or kubeconfig

Restrict Argo CD admin access

✅ Final Workflow

Push code to GitHub

GitHub Actions (Self-Hosted Runner) builds image

Helm values updated

Argo CD auto-syncs

App deployed to k3s 🎉




