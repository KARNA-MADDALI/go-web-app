
# 🚀 GO Web Application – Project 2  
> Created by **Maddali Karna**

This project demonstrates the complete DevOps lifecycle using a Go web application, Docker, Kubernetes (EKS), Helm, GitHub Actions for CI, and Argo CD for GitOps-based CD.

---

## 📋 Project Plan

1. **Containerize** the Go application using a Dockerfile  
2. Write **Kubernetes manifests** (Deployment, Service, Ingress)  
3. Manage environments (Dev/QA/Prod) using **Helm charts**  
4. Set up **Ingress Controller** (Nginx) and expose using LoadBalancer  
5. Implement:
   - **CI:** GitHub Actions  
   - **CD:** GitOps using Argo CD  

---

## 🛠️ Step-by-Step Instructions

### 🔹 Step 1: Local Setup

1. Make sure **Go** is installed on your system
2. Run the following commands to test locally:

```bash
go build -o main
go run main
```

3. Open in browser: [http://localhost:8080/courses](http://localhost:8080/courses)

---

### 🐳 Step 2: Dockerize the Application

**Dockerfile:**
```Dockerfile
FROM golang:1.22
WORKDIR /app
COPY . .
RUN go build -o main .
CMD ["./main"]
```

**Build and Push:**
```bash
docker build -t my-go-app .
docker tag my-go-app karna123/go-web-app:v1
docker push karna123/go-web-app:v1
```

---

### ☸️ Step 3: Deploy to Kubernetes (EKS)

1. Create `k8s/manifests/` folder and add:
   - `deployment.yaml`
   - `service.yaml`
   - `ingress.yaml`

2. Create EKS Cluster:
```bash
eksctl create cluster --name demo-cluster --region us-east-1
```

3. Apply manifests:
```bash
kubectl apply -f k8s/manifests/deployment.yaml
kubectl apply -f k8s/manifests/service.yaml
kubectl apply -f k8s/manifests/ingress.yaml
```

4. Verify resources:
```bash
kubectl get pods
kubectl get deployments
kubectl get svc
kubectl get ing
```

5. Delete resources (if needed):
```bash
kubectl delete -f k8s/manifests/ingress.yaml
kubectl delete -f k8s/manifests/service.yaml
kubectl delete -f k8s/manifests/deployment.yaml
```

---

### 🌐 Step 4: Setup Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.1/deploy/static/provider/aws/deploy.yaml
```

Verify IP:
```bash
kubectl get ing
```

Assign to local DNS:
```bash
nslookup <your-ingress-dns>
sudo vim /etc/hosts  # Add the IP with domain
```

---

### 📦 Step 5: Helm Chart Setup

1. Create and clean Helm chart:
```bash
helm create go-web-app-chart
cd go-web-app-chart
rm -rf charts/
cd templates/
rm -rf *
cp ../../../k8s/manifests/* .
```

2. Define dynamic values in `values.yaml`

3. Install the app:
```bash
helm install go-web-app ./go-web-app-chart
```

4. To uninstall:
```bash
helm uninstall go-web-app
```

---

## ⚙️ CI: GitHub Actions

1. Create `.github/workflows/ci.yaml`
2. Define pipeline steps:
   - Unit tests
   - Build
   - Static code analysis
   - Docker build and push

3. Set GitHub secrets:
   - `DOCKER_USERNAME`
   - `DOCKER_PASSWORD`
   - `GITHUB_TOKEN`

4. Configure git and push:
```bash
git init
git add .
git commit -m "CI pipeline added"
git remote add origin https://github.com/KARNA-MADDALI/go-web-app.git
git push -u origin main
```

> Every commit to `main` triggers the pipeline

---

## 🚀 CD: Argo CD (GitOps)

### 1. Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### 2. Access Argo CD UI

```bash
kubectl get svc -n argocd
kubectl get secret argocd-initial-admin-secret -n argocd
kubectl edit secret argocd-initial-admin-secret -n argocd
```

Decode admin password:
```bash
echo <encoded_password> | base64 --decode
```

Login:
- **Username:** `admin`
- **Password:** decoded value above

---

### 3. Create Application in Argo CD

1. Click on `New App` in Argo CD dashboard  
2. Provide:
   - App Name
   - Git Repo URL
   - Path to Helm chart
   - Target cluster
   - Namespace

3. Click **Create** → Argo CD monitors the repo for updates and auto-deploys on changes.
4. NOw we can access the api using this url http://go-web-app.local/courses 

---

## 📌 Summary

| Tool        | Purpose                            |
|-------------|------------------------------------|
| Go          | Backend language                   |
| Docker      | Containerization                   |
| Kubernetes  | Orchestration                      |
| Helm        | Manage multi-env configurations    |
| GitHub CI   | Build, test, push image            |
| Argo CD     | GitOps-based Continuous Deployment |

---

## 🙌 Credits

**Maddali Karna**  
DevOps Enthusiast & Cloud Learner  
[LinkedIn](https://linkedin.com/in/maddalikarna) • [GitHub](https://github.com/maddalikarna)


