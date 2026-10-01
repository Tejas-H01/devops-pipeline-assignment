# End-to-End DevOps Pipeline using Docker, Jenkins, Kubernetes & Monitoring

## Project Overview
This project demonstrates a complete **DevOps Continuous Integration & Continuous Deployment (CI/CD)** workflow for the **NewsHub** web application. It integrates industry-standard tools including **GitHub**, **Jenkins**, **Docker**, **Kubernetes**, and **Prometheus** for automated testing, containerization, local deployment, and monitoring.

---

## 📁 Repository Structure
```text
End-to-End-DevOps-Pipeline/
├── app/
│   └── index.html          # NewsHub web application source code
├── Dockerfile              # Nginx-based container build file
├── Jenkinsfile             # 7-stage Jenkins CI/CD pipeline definition
├── k8s/
│   ├── deployment.yaml     # Kubernetes Deployment manifest (2 replicas)
│   └── service.yaml        # Kubernetes NodePort Service manifest
├── monitoring/
│   └── prometheus.yml      # Prometheus scraping configuration
└── README.md               # Complete assignment documentation
```

---

## ⚙️ DevOps Workflow Architecture

```text
[ Developer Push ]
       ↓
[ GitHub Repository ]
       ↓
[ Jenkins CI/CD Pipeline ]
       ├─ Stage 1: Checkout Source
       ├─ Stage 2: Validate Application Files
       ├─ Stage 3: Build Docker Image (devops-web:latest)
       ├─ Stage 4: Cleanup Old Container (devops-web-container)
       ├─ Stage 5: Deploy Docker Container (Port 8082:80)
       ├─ Stage 6: Verify Deployment (HTTP 200 OK Ping)
       └─ Stage 7: Deploy to Kubernetes (Optional)
       ↓
[ Live Application at http://localhost:8082 ]
```

---

## 🚀 How to Run Locally

### 1. Manual Docker Build & Deployment
```bash
# Build the Docker image
docker build -t devops-web .

# Stop and remove any existing container instance
docker stop devops-web-container
docker rm devops-web-container

# Run container mapped to port 8082
docker run -d --name devops-web-container -p 8082:80 devops-web

# Verify active container
docker ps --filter "name=devops-web-container"

# Access application in browser or terminal
http://localhost:8082
```

### 2. Jenkins Automated Pipeline Setup
1. Launch **Jenkins** (via Docker or standalone WAR/Service on port `8080`).
2. Install **Git** and **Pipeline** plugins.
3. Create a **New Item** → Select **Pipeline**.
4. Under **Pipeline Definition**, select **Pipeline script from SCM**.
5. Set SCM to **Git** and enter this repository path.
6. Click **Save** and **Build Now**.

---

## 📊 Verification Commands
```bash
# Check Docker image
docker images devops-web

# Check running container
docker ps --filter "name=devops-web-container"

# Test local web server response
powershell -Command "(Invoke-WebRequest -Uri 'http://localhost:8082' -UseBasicParsing).StatusCode"
```
