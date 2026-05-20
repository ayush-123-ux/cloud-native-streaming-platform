<div align="center">

# 🎬 Cloud Native Streaming Platform

### A Production-Grade Amazon Prime Video Clone — Built for the Cloud, Secured for the Enterprise

<br/>

[![React](https://img.shields.io/badge/React-18.2-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://reactjs.org/)
[![Docker](https://img.shields.io/badge/Docker-Node:Alpine-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docker.com)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?style=for-the-badge&logo=jenkins&logoColor=white)](https://jenkins.io/)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://terraform.io/)
[![AWS](https://img.shields.io/badge/AWS-EKS%20%7C%20EC2%20%7C%20IAM-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![SonarQube](https://img.shields.io/badge/SonarQube-Quality%20Gate-4E9BCD?style=for-the-badge&logo=sonarqube&logoColor=white)](https://sonarqube.org/)
[![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Grafana-Dashboards-F46800?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com/)
[![Trivy](https://img.shields.io/badge/Trivy-Security%20Scan-1904DA?style=for-the-badge&logo=aquasecurity&logoColor=white)](https://trivy.dev/)

<br/>

> Fully automated **DevSecOps pipeline** — from code commit to live Kubernetes deployment on AWS EKS,  
> with security scanning baked in at every single stage.

</div>

---

## 🗺️ Architecture

```
                    ┌──────────────────────────────────────────────────────────────┐
                    │                    AWS Cloud  (eu-north-1)                   │
                    │                                                              │
  git push  ──────► │   ┌─────────────────────────────────────────────────────┐   │
                    │   │               Jenkins CI/CD Pipeline                 │   │
                    │   │                                                      │   │
                    │   │  ① Clean  →  ② Git Checkout  →  ③ SonarQube        │   │
                    │   │                                       ↓             │   │
                    │   │                               Quality Gate ✅        │   │
                    │   │                                       ↓             │   │
                    │   │              ④ npm install  →  ⑤ Trivy FS Scan     │   │
                    │   │                                       ↓             │   │
                    │   │        ⑥ Docker Build  →  ⑦ Docker Scout (CVE)    │   │
                    │   │                ↓                      ↓             │   │
                    │   │       ⑧ Trivy Image Scan  →  ⑨ Push Docker Hub    │   │
                    │   └───────────────────────────────────────┬─────────────┘   │
                    │                                           │                  │
                    │   ┌───────────────────────────────────────▼─────────────┐   │
                    │   │              Jenkins Pipeline 2 (EKS Deploy)        │   │
                    │   │                                                      │   │
                    │   │   AWS Auth Check  →  kubectl config  →  apply ✅    │   │
                    │   └───────────────────────────────────────┬─────────────┘   │
                    │                                           │                  │
                    │               ┌───────────────────────────▼──────────────┐  │
                    │               │           AWS EKS Cluster                │  │
                    │               │                                          │  │
                    │               │   ┌─────────────┐  ┌─────────────┐      │  │
                    │               │   │   Pod  1    │  │   Pod  2    │      │  │
                    │               │   │ React :3000 │  │ React :3000 │      │  │
                    │               │   └──────┬──────┘  └──────┬──────┘      │  │
                    │               │          └────────┬────────┘             │  │
                    │               │      LoadBalancer Service (:80)          │  │
                    │               └──────────────────────────────────────────┘  │
                    │                                                              │
                    │   ┌──────────────────────────────────────────────────────┐  │
                    │   │          Monitoring Stack  (Terraform EC2)           │  │
                    │   │   Prometheus :9090  │  Grafana :3000  │  Node :9100  │  │
                    │   │   Blackbox Exporter :9115  │  Jenkins Metrics :8080  │  │
                    │   └──────────────────────────────────────────────────────┘  │
                    └──────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| 🖥️ **Frontend** | React 18, CSS3 |
| 🐳 **Container** | Docker, Node:Alpine |
| ☸️ **Orchestration** | Kubernetes, AWS EKS |
| ⚙️ **CI/CD** | Jenkins (2 Pipelines) |
| 🏗️ **IaC** | Terraform (AWS Provider ~5.0) |
| 🔒 **Security** | SonarQube, Trivy, Docker Scout |
| 📊 **Monitoring** | Prometheus, Grafana, Blackbox Exporter, Node Exporter |
| ☁️ **Cloud** | AWS EKS, EC2, IAM, VPC, Security Groups |
| 📦 **Registry** | Docker Hub (`ayus654/amazon-prime-video`) |

---

## ✨ Highlights

| | |
|---|---|
| ⚡ **Dual Jenkins Pipelines** | Separate pipelines for build/security and EKS deployment |
| 🔒 **Shift-Left Security** | Vulnerabilities caught pre-build, post-build, and at code level |
| ☸️ **High Availability** | 2-replica Kubernetes deployment with RollingUpdate strategy |
| 📊 **Full Observability** | Metrics, dashboards, and HTTP probing from day one |
| 🏗️ **Infra as Code** | Entire monitoring server provisioned via Terraform |
| 📬 **Smart Notifications** | Email alerts with Trivy scan reports attached on every build |
| 🐳 **Lean Image** | Node:Alpine base keeps Docker image size minimal |

---

## 🔄 CI/CD Pipeline — Deep Dive

### ⚙️ Pipeline 1 · Build · Scan · Push  (`Jenkinsfile`)

```
 ┌─────────────────┐    ┌──────────────────┐    ┌──────────────────────┐
 │  Clean Workspace│───►│  Git Checkout    │───►│  SonarQube Analysis  │
 └─────────────────┘    └──────────────────┘    └──────────┬───────────┘
                                                            │
                                                  Quality Gate (abort on fail)
                                                            │
 ┌─────────────────┐    ┌──────────────────┐    ┌──────────▼───────────┐
 │  Trivy FS Scan  │◄───│  npm install     │◄───│  Dependencies Ready  │
 │  → trivyfs.txt  │    └──────────────────┘    └──────────────────────┘
 └────────┬────────┘
          │
 ┌────────▼────────┐    ┌──────────────────┐    ┌──────────────────────┐
 │  Docker Build   │───►│  Docker Scout    │───►│  Trivy Image Scan    │
 │  & Tag          │    │  CVE + Recs      │    │  → trivyimage.txt    │
 └────────┬────────┘    └──────────────────┘    └──────────────────────┘
          │
 ┌────────▼──────────────────┐    ┌──────────────────────────────────────┐
 │  Push → Docker Hub        │───►│  Deploy Container  (port 3000)       │
 │  ayus654/amazon-prime:latest│  └──────────────────────────────────────┘
 └───────────────────────────┘
          │
 ┌────────▼───────────────────────────────────┐
 │  📧 Email: Status + trivyfs + trivyimage   │
 └────────────────────────────────────────────┘
```

### ☸️ Pipeline 2 · Deploy to EKS  (`Jenkinsfile2-eks`)

```
 Clean Workspace  →  Git Checkout  →  aws sts get-caller-identity ✅
         →  aws eks update-kubeconfig (eu-north-1 / amazon-prime)
         →  kubectl apply -f manifest.yml
         →  kubectl get pods / get svc  ✅
         →  📧 Email Notification
```

---

## 🛡️ DevSecOps — Security at Every Stage

```
  Code           Pre-Build          Post-Build         Registry
  ────           ─────────          ──────────         ────────
    │                │                   │                 │
 SonarQube       Trivy FS           Trivy Image       Docker Scout
 Code Quality    Dependency         Container CVE     CVE + Fixes
 Bugs & Smells   Vulnerabilities    Scanning          Recommendations
    │                │                   │                 │
    └────────────────┴───────────────────┴─────────────────┘
                         All results emailed as artifacts
```

| Tool | Scans | Stage |
|---|---|---|
| **SonarQube** | Code quality, bugs, code smells | Every commit |
| **Trivy FS** | Source code & dependencies | Pre-build |
| **Trivy Image** | Docker image CVEs | Post-build |
| **Docker Scout** | CVEs + remediation advice | Post-build |

---

## 📊 Monitoring Stack

```
  App / Jenkins / Host
         │
         ▼
  ┌─────────────────────────────────────────────┐
  │              Prometheus  :9090              │
  │                                             │
  │  Scrape Jobs:                               │
  │  • node_exporter  :9100  (CPU/RAM/Disk)     │
  │  • blackbox       :9115  (HTTP probing)     │
  │  • jenkins        :8080/prometheus          │
  └────────────────────┬────────────────────────┘
                       │
                       ▼
              Grafana  :3000
              (Dashboards & Alerts)
```

| Component | Role | Port |
|---|---|---|
| **Prometheus** | Metrics scraping & storage | 9090 |
| **Grafana** | Visualization & alerting | 3000 |
| **Node Exporter** | Host metrics (CPU, RAM, Disk) | 9100 |
| **Blackbox Exporter** | HTTP/HTTPS endpoint probing | 9115 |
| **Jenkins Metrics** | Pipeline health metrics | 8080/prometheus |

> Entire monitoring server is auto-provisioned by **Terraform** on `t2.medium` EC2.

---

## 📁 Project Structure

```
cloud-native-streaming-platform/
│
├── 📦 src/components/          # React UI components
│   ├── App.jsx
│   ├── HeaderComp.jsx
│   ├── BodyComp.jsx
│   ├── ContentCarousel.jsx
│   └── FooterComp.jsx
│
├── 🐳 Dockerfile               # Node:Alpine · port 3000
├── ⚙️  Jenkinsfile              # Pipeline 1: Build → Scan → Push
├── ⚙️  Jenkinsfile2-eks         # Pipeline 2: EKS Deploy
│
├── ☸️  kubernetes/
│   └── manifest.yml            # Deployment (2 replicas) + LoadBalancer
│
├── 🏗️  terraform/
│   ├── main.tf                 # EC2 monitoring server · eu-north-1
│   ├── variables.tf
│   └── output.tf
│
├── 📊 monitoring/
│   └── Promethues.yml          # Prometheus scrape config
│
└── 📜 scripts/                 # One-click install scripts
    ├── docker.sh · jenkins.sh · kubectl.sh · eksctl.sh
    ├── prometheus.sh · grafana.sh · terraform.sh · trivy.sh
```

---

## 🚀 Quick Start

### Run Locally

```bash
git clone https://github.com/ayush-123-ux/cloud-native-streaming-platform.git
cd cloud-native-streaming-platform
npm install && npm start
# → http://localhost:3000
```

### Run with Docker

```bash
docker build -t amazon-prime-video .
docker run -d -p 3000:3000 amazon-prime-video

# or pull directly
docker run -d -p 3000:3000 ayus654/amazon-prime-video:latest
```

### Deploy to EKS

```bash
aws eks update-kubeconfig --region eu-north-1 --name amazon-prime
kubectl apply -f kubernetes/manifest.yml
kubectl get pods && kubectl get svc
```

### Provision Monitoring (Terraform)

```bash
cd terraform/
terraform init
terraform plan
terraform apply          # → outputs EC2 public IP
```

### Install All Tools

```bash
chmod +x scripts/*.sh
./scripts/docker.sh && ./scripts/jenkins.sh && ./scripts/kubectl.sh
./scripts/eksctl.sh && ./scripts/prometheus.sh && ./scripts/grafana.sh
```

---

## 🎯 What This Project Demonstrates

- ✅ End-to-end **DevSecOps pipeline** design and implementation
- ✅ **Multi-stage security scanning** (code → filesystem → image → registry)
- ✅ **Kubernetes** production deployment on managed AWS EKS
- ✅ **Infrastructure as Code** with Terraform for repeatable environments
- ✅ **Observability** setup with Prometheus, Grafana & Blackbox Exporter
- ✅ **Automated notifications** with build artifacts on every pipeline run
- ✅ Containerization best practices with lightweight Docker images

---

<div align="center">

**👤 Ayush** · [@ayush-123-ux](https://github.com/ayush-123-ux)

<br/>

⭐ **Star this repo if it helped you!**

</div>
