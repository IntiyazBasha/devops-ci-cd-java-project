# End-to-End CI/CD Pipeline for Java Application

## 📌 Project Overview

This project demonstrates the implementation of a complete **end-to-end CI/CD pipeline** for a Java-based application using modern DevOps tools.  
The backend application and database source code were taken from GitHub and deployed using a fully automated pipeline.

The pipeline automates code checkout, build, code quality analysis, artifact storage, containerization, security scanning, deployment, and monitoring on AWS EC2.

---

## 🔁 CI/CD Pipeline Flow

GitHub → Jenkins → Maven Build → SonarQube → Quality Gate → Nexus →  
Docker Build → Trivy Scan → Docker Compose Deploy → Email Notification → Monitoring (New Relic)

---

## 🧰 Technology Stack

- Git – Source code management  
- Jenkins – CI/CD automation  
- Maven – Build and dependency management  
- SonarQube – Code quality analysis  
- Nexus Repository Manager – Artifact repository  
- Docker – Containerization  
- Docker Compose – Multi-container orchestration  
- Trivy – Vulnerability scanning  
- Tomcat – Application server  
- New Relic – Application performance monitoring  
- AWS EC2 – Infrastructure (Amazon Linux)  

---

## 🏗 Infrastructure Setup

- AWS EC2 instance with Amazon Linux AMI  
- Instance Type: 2 vCPU, 8 GB RAM  
- Storage: 28 GB  
- Security Group: All traffic allowed (for learning/practice purposes only)  

---

## ⚙ Implementation Steps

### 1️⃣ EC2 Provisioning
An EC2 instance was launched on AWS using Amazon Linux with sufficient CPU, memory, and storage to support Jenkins, Docker, SonarQube, and monitoring tools.

---

### 2️⃣ Installation of Git, Docker & Docker Compose
Git and Docker were installed on the EC2 instance.  
Docker Compose was installed using a supported method (binary or pip) based on availability.

---

### 3️⃣ Jenkins Installation & Configuration
- Jenkins installed using the official Jenkins repository  
- Java 17 (Amazon Corretto) configured  
- Required plugins installed  
- Jenkins workspace stability ensured by mounting a dedicated `/tmp` directory  

Jenkins was accessed via: http://<EC2-Public-IP>:8080

---

### 4️⃣ SonarQube Integration
- SonarQube deployed as a Docker container  
- Jenkins integrated with SonarQube Scanner plugin  
- Authentication configured using Jenkins credentials  
- Webhook configured in SonarQube  

**Quality Gates were enforced**, and the pipeline automatically fails if the quality gate does not pass.

---

### 5️⃣ Trivy Vulnerability Scanning
Trivy was installed on the server and integrated into the pipeline to scan Docker images for known vulnerabilities (CVEs).

---

### 6️⃣ Nexus Repository Integration
- Nexus Repository Manager installed on a separate EC2 instance  
- Maven hosted repository created  
- Jenkins configured to upload WAR artifacts to Nexus after successful builds  

---

### 7️⃣ Jenkins Email Notifications
Email Extension plugin configured to send build status notifications after every pipeline execution.

---

### 8️⃣ Maven Integration
Maven configured in Jenkins under Global Tool Configuration and used for building and packaging the Java application.

---

### 9️⃣ Containerization & Deployment
- Application and database containerized using Docker  
- Custom Dockerfiles created for application and MySQL database  
- Multi-container deployment orchestrated using Docker Compose

---

### 🔟 Monitoring with New Relic
After successful deployment using Docker Compose, **New Relic APM** was integrated into the application container to monitor runtime performance.

The New Relic Java agent was configured to collect:
- Application response time and throughput
- Error rates and failed transactions
- JVM performance metrics
- CPU and memory utilization

This enabled real-time observability and post-deployment monitoring of the application.

## 🐳 Docker Configuration

### Application Dockerfile
```dockerfile
FROM tomcat:8-jre11
RUN rm -rf /usr/local/tomcat/webapps/*
COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
CMD ["catalina.sh", "run"]

### Database Dockerfile
FROM mysql:5.7.25
ENV MYSQL_ROOT_PASSWORD="devopspassword"
ENV MYSQL_DATABASE="accounts"
ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql


## 🧩 Docker Compose Configuration
```yaml
version: "3"
services:
  devopsdb:
    container_name: devopsdb
    image: dbimage
    ports:
      - "3306:3306"

  application:
    container_name: appcontainer
    image: appimage
    ports:
      - "1111:8080"
    depends_on:
      - devopsdb

---
## 🧪 Jenkins Pipeline
The Jenkins pipeline was written using Declarative Pipeline syntax and includes the following stages:
Clean Workspace
Code Checkout
Build using Maven
Code Quality Analysis (SonarQube)
Quality Gate Enforcement
Artifact Upload to Nexus
Docker Image Build
Trivy Image Scan
Deployment using Docker Compose
Email Notification
---
## 🏁 Outcome
Fully automated CI/CD pipeline
Integrated quality, security, and monitoring checks
Containerized deployment using Docker and Docker Compose
Automated artifact management using Nexus
Real-time application monitoring using New Relic
Complete DevOps lifecycle implemented:
Build → Test → Deploy → Monitor
---
