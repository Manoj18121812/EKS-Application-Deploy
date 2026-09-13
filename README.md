# 🚀 Two-Tier Spring Boot Application on AWS EKS

A production-style **two-tier CRUD application** deployed on **Amazon EKS** with a complete CI/CD pipeline using **Jenkins, Docker, GitHub, Kubernetes, Prometheus, and Grafana**.

The project demonstrates containerization, Kubernetes orchestration, persistent storage, autoscaling, ingress, monitoring, rolling updates, and automated deployments.

---

## 🏗️ Architecture

```text
                         ┌─────────────────┐
                         │     Developer   │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │     GitHub      │
                         │   Source Code   │
                         └────────┬────────┘
                                  │
                              Webhook
                                  │
                                  ▼
                         ┌─────────────────┐
                         │     Jenkins     │
                         │                 │
                         │ Maven Build     │
                         │ Unit Tests      │
                         │ Docker Build    │
                         │ Docker Push     │
                         │ EKS Deploy      │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   Docker Hub    │
                         │ Container Image  │
                         └────────┬────────┘
                                  │
                                  ▼
                    ┌──────────────────────────┐
                    │       AWS EKS Cluster    │
                    │                          │
                    │  ┌────────────────────┐  │
                    │  │ NGINX Ingress      │  │
                    │  └─────────┬──────────┘  │
                    │            │             │
                    │  ┌─────────▼──────────┐  │
                    │  │ Spring Boot App    │  │
                    │  │ Deployment         │  │
                    │  │                    │  │
                    │  │ Pod 1              │  │
                    │  │ Pod 2              │  │
                    │  │ Pod N              │  │
                    │  └─────────┬──────────┘  │
                    │            │             │
                    │       Kubernetes        │
                    │         Service          │
                    │            │             │
                    │  ┌─────────▼──────────┐  │
                    │  │ MySQL StatefulSet  │  │
                    │  │                    │  │
                    │  │ MySQL Pod          │  │
                    │  │        │           │  │
                    │  │        ▼           │  │
                    │  │    PVC / EBS       │  │
                    │  └────────────────────┘  │
                    │                          │
                    │  HPA + Metrics Server    │
                    └──────────────────────────┘

                    Monitoring
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
        ┌─────────────┐       ┌─────────────┐
        │ Prometheus  │──────▶│  Grafana    │
        └─────────────┘       └─────────────┘
```

---

# 📌 Project Overview

This project deploys a **Spring Boot Student Management CRUD application** on Amazon EKS.

The application consists of:

* Spring Boot backend
* Thymeleaf frontend
* MySQL database
* Kubernetes Deployment
* Kubernetes Service
* StatefulSet
* Persistent Volume
* ConfigMap
* Secret
* NGINX Ingress
* Horizontal Pod Autoscaler
* Prometheus
* Grafana
* Jenkins CI/CD

The deployment process is completely automated:

```text
Git Push
   ↓
GitHub Webhook
   ↓
Jenkins
   ↓
Maven Build & Test
   ↓
Docker Build
   ↓
Docker Hub Push
   ↓
EKS Deployment
   ↓
Rolling Update
   ↓
Application
```

---

# 🛠️ Technologies Used

| Technology     | Purpose                       |
| -------------- | ----------------------------- |
| Java 17        | Application runtime/build     |
| Spring Boot    | Backend application           |
| Thymeleaf      | Web UI                        |
| MySQL 8        | Database                      |
| Docker         | Containerization              |
| Kubernetes     | Container orchestration       |
| Amazon EKS     | Managed Kubernetes            |
| Amazon EBS     | Persistent database storage   |
| NGINX Ingress  | External application access   |
| HPA            | Application autoscaling       |
| Metrics Server | CPU metrics                   |
| Jenkins        | CI/CD                         |
| GitHub         | Source code management        |
| Docker Hub     | Container registry            |
| Prometheus     | Metrics collection            |
| Grafana        | Monitoring & dashboards       |
| Helm           | Kubernetes package management |

---

# 📂 Project Structure

```text
Two-tier-Crud-CICD/
│
├── pom.xml
├── Dockerfile
├── Jenkinsfile
├── README.md
│
├── src/
│   └── main/
│       └── resources/
│           ├── application.properties
│           └── templates/
│               ├── index.html
│               ├── new_student.html
│               └── edit_student.html
│
└── k8s/
    ├── deployment.yaml
    ├── service.yaml
    ├── configmap.yaml
    ├── secret.yaml
    ├── mysql.yaml
    ├── ingress.yaml
    └── hpa.yaml
```

---

# ☁️ AWS Infrastructure

## EKS Cluster

Cluster:

```text
two-tier-eks
```

Region:

```text
us-east-1
```

Node group:

```text
worker-nodes
```

Instance type:

```text
t3.medium
```

Initial nodes:

```text
2
```

Autoscaling:

```text
Minimum: 2
Maximum: 4
```

Check cluster:

```bash
kubectl get nodes
```

Expected:

```text
NAME                            STATUS   ROLES    AGE
ip-192-168-0-160.ec2.internal   Ready    <none>   ...
ip-192-168-49-73.ec2.internal   Ready    <none>   ...
```

---

# 🐳 Docker

Docker image:

```text
manoj1812/two-tier-springboot
```

Example image:

```text
manoj1812/two-tier-springboot:1.2
```

CI/CD images are generated using Jenkins build numbers:

```text
manoj1812/two-tier-springboot:build-1
manoj1812/two-tier-springboot:build-2
manoj1812/two-tier-springboot:build-3
```

Dockerfile:

```dockerfile
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

COPY target/Two-tier-Crud-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

---

# ☸️ Kubernetes Components

## Namespace

All application resources are deployed into:

```text
production
```

Create namespace:

```bash
kubectl create namespace production
```

---

# 🚀 Spring Boot Deployment

Deployment:

```text
two-tier-app
```

The application runs multiple replicas.

Example:

```yaml
replicas: 2
```

Rolling update strategy:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

This ensures that existing application pods remain available while the new version is deployed.

Check deployment:

```bash
kubectl get deployment -n production
```

Check pods:

```bash
kubectl get pods -n production
```

---

# 🔌 Kubernetes Service

Application Service:

```text
two-tier-app
```

Service type:

```text
ClusterIP
```

Port:

```text
80
```

Target port:

```text
8080
```

Traffic flow:

```text
NGINX Ingress
      ↓
two-tier-app Service :80
      ↓
Spring Boot Pod :8080
```

---

# 🗄️ MySQL StatefulSet

MySQL runs inside Kubernetes using a StatefulSet.

```text
StatefulSet
    ↓
mysql-0
    ↓
PVC
    ↓
AWS EBS
```

Database:

```text
studentdbcicd
```

MySQL image:

```text
mysql:8.0
```

Check MySQL:

```bash
kubectl get statefulset -n production
kubectl get pods -n production
```

---

# 💾 Persistent Storage

The MySQL database uses a PersistentVolumeClaim.

Storage:

```text
10Gi
```

StorageClass:

```text
gp3
```

Access mode:

```text
ReadWriteOnce
```

Check PVC:

```bash
kubectl get pvc -n production
```

Expected:

```text
mysql-data-mysql-0   Bound   ...   10Gi   RWO   gp3
```

The AWS EBS CSI Driver dynamically provisions EBS storage for the PVC.

---

# 🔐 ConfigMap

Non-sensitive database configuration is stored in a ConfigMap.

Example:

```yaml
DB_HOST: mysql
DB_NAME: studentdbcicd
```

Check:

```bash
kubectl get configmap -n production
kubectl describe configmap two-tier-config -n production
```

---

# 🔑 Kubernetes Secret

The MySQL root password is stored in a Kubernetes Secret.

Secret:

```text
mysql-secret
```

Check:

```bash
kubectl get secret -n production
```

The password is **not stored directly in the application configuration**.

Spring Boot receives it through an environment variable:

```text
DB_PASSWORD
```

---

# 🌐 NGINX Ingress

NGINX Ingress Controller provides external access to the application.

Traffic:

```text
Internet
   ↓
AWS Load Balancer
   ↓
NGINX Ingress Controller
   ↓
two-tier-app Service
   ↓
Spring Boot Pods
```

Check ingress:

```bash
kubectl get ingress -n production
```

Check NGINX:

```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

---

# 📈 Horizontal Pod Autoscaler

HPA automatically scales the Spring Boot application based on CPU utilization.

Configuration:

```text
Minimum replicas: 2
Maximum replicas: 4
Target CPU: 70%
```

Check HPA:

```bash
kubectl get hpa -n production
```

Example:

```text
NAME                REFERENCE               TARGETS   MINPODS   MAXPODS
two-tier-app-hpa    Deployment/two-tier-app 39%/70%   2         4
```

Metrics Server provides CPU metrics:

```bash
kubectl top pods -n production
kubectl top nodes
```

---

# 🔥 HPA Load Testing

A temporary BusyBox pod can generate traffic:

```bash
kubectl run load-generator \
  -n production \
  --image=busybox:1.36 \
  --restart=Never \
  -- /bin/sh -c "while true; do wget -q -O- http://two-tier-app/ > /dev/null; done"
```

Watch HPA:

```bash
kubectl get hpa -n production -w
```

Watch pods:

```bash
kubectl get pods -n production -w
```

After testing:

```bash
kubectl delete pod load-generator -n production
```

---

# 🔄 Rolling Updates

The Jenkins pipeline deploys a new Docker image using:

```bash
kubectl set image deployment/two-tier-app \
  two-tier-app=manoj1812/two-tier-springboot:build-X \
  -n production
```

Check rollout:

```bash
kubectl rollout status deployment/two-tier-app \
  -n production
```

Check rollout history:

```bash
kubectl rollout history deployment/two-tier-app \
  -n production
```

---

# ⏪ Automatic Rollback

The Jenkins pipeline verifies the deployment after updating the image.

If rollout fails:

```text
Deployment
    ↓
Rollout Verification
    ↓
Failure
    ↓
kubectl rollout undo
    ↓
Previous Version
```

Rollback command:

```bash
kubectl rollout undo deployment/two-tier-app \
  -n production
```

Verify:

```bash
kubectl rollout status deployment/two-tier-app \
  -n production
```

---

# 🔧 Jenkins CI/CD

Jenkins pipeline stages:

```text
1. Checkout
2. Build & Test
3. Docker Build
4. Docker Push
5. Deploy to EKS
6. Verify Deployment
```

### Checkout

Jenkins checks out the GitHub repository.

### Build & Test

Maven builds the Spring Boot application.

Java 17 is used for Maven:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
./mvnw clean package
```

### Docker Build

Jenkins builds the Docker image:

```bash
docker build \
  -t manoj1812/two-tier-springboot:${IMAGE_TAG} \
  -t manoj1812/two-tier-springboot:latest .
```

### Docker Push

The image is pushed to Docker Hub.

### Deploy

Jenkins updates the Kubernetes Deployment:

```bash
kubectl set image deployment/two-tier-app ...
```

### Verify

Jenkins waits for:

```bash
kubectl rollout status
```

If the rollout fails, automatic rollback is executed.

---

# 🔗 GitHub Webhook

GitHub is configured with a Jenkins webhook.

Flow:

```text
git push
   ↓
GitHub
   ↓
Webhook
   ↓
Jenkins
   ↓
Pipeline starts automatically
```

Therefore, a developer does not need to manually start the Jenkins job for every code change.

---

# 📊 Monitoring

Monitoring stack:

```text
Prometheus
     ↓
Metrics Collection
     ↓
Grafana
     ↓
Dashboards
```

Installed using Helm:

```bash
helm install monitoring \
  prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace
```

Check monitoring:

```bash
kubectl get pods -n monitoring
```

---

# 📈 Grafana Dashboards

The following dashboards are configured:

```text
15757 - Global
15758 - Namespaces
15759 - Pods
15760 - Nodes
15761 - Deployments
1860  - Node Exporter Full
```

Grafana can be accessed using port forwarding:

```bash
kubectl port-forward \
  svc/monitoring-grafana \
  -n monitoring \
  3000:80 \
  --address 0.0.0.0
```

Prometheus:

```bash
kubectl port-forward \
  svc/monitoring-kube-prometheus-prometheus \
  -n monitoring \
  9090:9090 \
  --address 0.0.0.0
```

---

# 🧪 Useful Kubernetes Commands

### Cluster

```bash
kubectl get nodes
kubectl cluster-info
```

### Pods

```bash
kubectl get pods -n production
kubectl describe pod <pod-name> -n production
kubectl logs <pod-name> -n production
```

### Deployment

```bash
kubectl get deployment -n production
kubectl describe deployment two-tier-app -n production
kubectl rollout status deployment/two-tier-app -n production
kubectl rollout history deployment/two-tier-app -n production
```

### Service

```bash
kubectl get svc -n production
```

### Ingress

```bash
kubectl get ingress -n production
kubectl describe ingress two-tier-ingress -n production
```

### HPA

```bash
kubectl get hpa -n production
kubectl describe hpa two-tier-app-hpa -n production
```

### Storage

```bash
kubectl get pvc -n production
kubectl get pv
kubectl get storageclass
```

### Events

```bash
kubectl get events -n production --sort-by=.lastTimestamp
```

---

# 🐛 Troubleshooting

## Check application logs

```bash
kubectl logs deployment/two-tier-app \
  -n production
```

## Check MySQL logs

```bash
kubectl logs mysql-0 -n production
```

## Check pod details

```bash
kubectl describe pod <pod-name> -n production
```

## Check services

```bash
kubectl get svc -n production
```

## Check endpoints

```bash
kubectl get endpoints -n production
```

## Check resource usage

```bash
kubectl top pods -n production
kubectl top nodes
```

## Check recent events

```bash
kubectl get events \
  -n production \
  --sort-by=.lastTimestamp
```

---

# 🔐 Security Considerations

The project demonstrates basic Kubernetes security practices:

* Database password stored in Kubernetes Secret
* Application configuration separated using ConfigMap
* Docker Hub authentication handled using Jenkins Credentials
* AWS access provided through EC2 IAM Role
* No static AWS access keys required by Jenkins
* Kubernetes namespace isolation
* Kubernetes resource requests and limits configured

For a production environment, additional controls such as AWS Secrets Manager, IAM Roles for Service Accounts, network policies, TLS, private clusters, image scanning, and stronger RBAC would be recommended.

---

# 🎯 Key DevOps Concepts Demonstrated

This project demonstrates practical understanding of:

```text
Linux
Docker
Git
GitHub
Jenkins
CI/CD
AWS
EKS
Kubernetes
Deployments
Services
StatefulSets
ConfigMaps
Secrets
PVC
EBS
Ingress
HPA
Metrics Server
Helm
Prometheus
Grafana
Rolling Updates
Rollback
Infrastructure & Monitoring
```

---

# 💼 Interview Explanation

### Short Version

> I deployed a two-tier Spring Boot CRUD application on Amazon EKS. The application uses Spring Boot for the backend and MySQL running as a StatefulSet with persistent EBS storage. I containerized the application using Docker and created a Jenkins CI/CD pipeline integrated with GitHub webhooks. Whenever code is pushed to GitHub, Jenkins automatically builds and tests the application using Maven, creates a Docker image, pushes it to Docker Hub, and deploys the new image to EKS. Kubernetes handles rolling updates and automatic rollback if deployment verification fails. I also implemented NGINX Ingress for external access, HPA for CPU-based autoscaling, and Prometheus/Grafana for monitoring.

---

# 🚀 Future Improvements

Possible production enhancements:

* HTTPS/TLS using AWS Load Balancer or cert-manager
* AWS Secrets Manager
* IRSA for Kubernetes workloads
* Network Policies
* Private EKS cluster
* Multi-AZ MySQL architecture
* Automated database backups
* Docker image vulnerability scanning
* SonarQube code quality analysis
* Argo CD / GitOps
* Centralized logging using Loki
* Alertmanager notifications
* Blue/Green or Canary deployments

---

# 👨‍💻 Author

**Manoj**

DevOps / Cloud Engineering Project

Technologies:

```text
AWS | EKS | Kubernetes | Docker | Jenkins | GitHub
Spring Boot | MySQL | Helm | Prometheus | Grafana
```

---

# ⭐ Project Highlights

```text
✅ AWS EKS
✅ Kubernetes Production-style Deployment
✅ Stateful MySQL + Persistent EBS Storage
✅ Docker Containerization
✅ Jenkins CI/CD
✅ GitHub Webhook Automation
✅ Docker Hub Registry
✅ Rolling Updates
✅ Automatic Rollback
✅ Horizontal Pod Autoscaling
✅ NGINX Ingress
✅ Prometheus Monitoring
✅ Grafana Dashboards
```
