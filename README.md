# Go Web Application

This is a simple website written in Golang. It uses the `net/http` package to serve HTTP requests.

## Running the server

To run the server, execute the following command:


go run main.go
```

The server will start on port 8080. You can access it by navigating to `http://localhost:8080/courses` in your web browser.

## Looks like this

![Website](static/images/golang-website.png)


Go Web App 🚀

A production-ready Go web application deployed on Kubernetes (k3s) using:

Docker

Helm

GitHub Actions 

Argo CD (GitOps)

This repository demonstrates a real-world DevOps workflow:

Code → CI → Docker Image Build → GitOps → Argo CD Sync → Kubernetes


🧰 Tech Stack

Language: Go (net/http)

Containerization: Docker

Kubernetes: k3s

Helm: Application packaging

GitOps CD: Argo CD

CI/CD: GitHub Actions

Ingress: Traefik (default with k3s)

Linting: golangci-lint


📂 Project Structure
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

Run locally:

go run main.go


Access: http://localhost:8080


🐳 Docker Build & Run
docker build -t go-web-app:latest .
docker run -p 8080:8080 go-web-app:latest


☸️ Kubernetes Setup (k3s)

1️⃣ Install k3s:

curl -sfL https://get.k3s.io | sh -



2️⃣ Verify cluster nodes:

kubectl get nodes -o wide


k3s includes:

Traefik (Ingress controller)

Containerd runtime

Service Load Balancer (ServiceLB)


🔁 CI Pipeline (GitHub Actions)

Builds Docker image

Pushes image to Docker Hub / Registry

Triggers Argo CD sync for GitOps deployment

📦 Helm Deployment
Install the app
helm install go-web-app ./helm/go-web-app-chart -n default

Upgrade the app
helm upgrade go-web-app ./helm/go-web-app-chart -n default


🔄 Argo CD Installation (GitOps)

Important: Argo CD continuously monitors the Git repository and recreates resources if they are deleted manually.
This means manual Helm installation or kubectl apply can fail if the resources already exist.

Steps:

1️⃣ Delete any existing Argo CD-managed resources (if any):

helm uninstall go-web-app -n default
kubectl delete deployment go-web-app -n default
kubectl delete svc go-web-app -n default
kubectl delete ingress go-web-app -n default


This ensures Helm can take full ownership later.


2️⃣ Create Argo CD namespace:

kubectl create namespace argocd


3️⃣ Install Argo CD:

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


4️⃣ Wait for pods to be ready:

kubectl get pods -n argocd

🔑 Access Argo CD Dashboard

Find Argo CD service NodePort:

kubectl get svc -n argocd


Open browser: http://<NodePort-IP>:<Port>

Click Advanced → Continue Unsafe

Login:

Username: admin
Password: 
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo

🔧 Configure Argo CD via Dashboard

Go to Settings → Repositories

SSH: paste your private key in Private Key Data

HTTP/HTTPS: no key required

Create Application:

Application Name: go-web-app

Project: default

Repository: your Git repo

Path: relative path to Helm chart (e.g., ./helm/go-web-app-chart)

Destination: Cluster = https://kubernetes.default.svc, Namespace = default

Enable Auto-Sync & Self-Heal in the application settings

🔁 Sync App

Click Sync in dashboard

Or via CLI:

argocd app sync go-web-app


Enable automated sync (recommended):

argocd app set go-web-app --sync-policy automated


Once enabled, Argo CD will continuously ensure the app matches Git. Any manual deletion of Deployment, Service, or Ingress will be recreated automatically.

🌐 Ingress (Traefik)

Example host: go-web-app.local

Add to /etc/hosts:

127.0.0.1 go-web-app.local


kubectl get svc -n default

Paste go-web-app.local:NodePort(port) on browser to check an application is working

🔐 Security Notes

Use GitHub Secrets for Docker registry credentials

Do not commit tokens or kubeconfig

Restrict Argo CD admin access

✅ Final Workflow

Push code to GitHub

GitHub Actions builds Docker image

Helm chart values updated

Argo CD auto-syncs from Git → App deployed to k3s 🎉

⚡ Key Argo CD Behavior

Continuous Reconciliation: Argo CD monitors Git and will recreate any deleted resources automatically.

Manual Helm install fails if Argo CD-created resources exist due to ownership metadata conflicts.

To switch to Helm:

Delete Argo CD app and resources

Install app via Helm

Argo CD ensures your cluster always matches Git after initial setup.

This version:

Uses dashboard instructions for Argo CD setup

Clearly warns about automatic resource recreation

Explains why Helm can fail if Argo CD already manages resources

