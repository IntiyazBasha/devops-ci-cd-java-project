# End-to-End CI/CD Pipeline for Java Application

## 📌 Project Overview

This project demonstrates the implementation of a complete **end-to-end CI/CD pipeline** for a Java-based application using modern DevOps tools.  
The backend application and database source code were taken from GitHub and deployed using a fully automated pipeline.

The pipeline automates code checkout, build, code quality analysis, artifact storage, containerization, security scanning, and deployment on AWS EC2.

---

## 🔁 CI/CD Pipeline Flow

GitHub → Jenkins → Maven Build → SonarQube → Quality Gate → Nexus →  
Docker Build → Trivy Scan → Docker Compose Deploy → Email Notification

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
An EC2 instance was launched on AWS using Amazon Linux with sufficient CPU, memory, and storage to support Jenkins, Docker, and SonarQube.

---

### 2️⃣ Installation of Git, Docker & Docker Compose
Git and Docker were installed on the EC2 instance.  
Docker Compose was installed using a supported method (binary or pip) based on availability.

---

### 3️⃣ Jenkins Installation & Configuration
- Jenkins was installed using the official Jenkins repository.
- Java 17 (Amazon Corretto) was configured.
- Required plugins were installed.
- Jenkins workspace stability was ensured by mounting a dedicated `/tmp` directory.

Jenkins was accessed via:
http://<EC2-Public-IP>:8080


---

### 4️⃣ SonarQube Integration
- SonarQube was deployed as a Docker container.
- Jenkins was integrated with SonarQube using the SonarQube Scanner plugin.
- Authentication was configured using Jenkins credentials.
- Webhook was configured in SonarQube.

**Quality Gates were enforced**, and the pipeline automatically fails if the quality gate does not pass.

---

### 5️⃣ Trivy Vulnerability Scanning
Trivy was installed on the server and integrated into the pipeline to scan Docker images for known vulnerabilities (CVEs).

---

### 6️⃣ Nexus Repository Integration
- Nexus Repository Manager was installed on a separate EC2 instance.
- A Maven hosted repository was created.
- Jenkins was configured to upload WAR artifacts to Nexus after successful builds.

---

### 7️⃣ Jenkins Email Notifications
Email Extension plugin was configured to send build status notifications after every pipeline execution.

---

### 8️⃣ Maven Integration
Maven was configured in Jenkins under Global Tool Configuration and used for building and packaging the Java application.

---

## 🐳 Docker Configuration

### Application Dockerfile
```dockerfile
FROM tomcat:8-jre11
RUN rm -rf /usr/local/tomcat/webapps/*
COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
CMD ["catalina.sh", "run"]
**Database Dockerfile**
FROM mysql:5.7.25
ENV MYSQL_ROOT_PASSWORD="devopspassword"
ENV MYSQL_DATABASE="accounts"
ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql
🧩 Docker Compose Configuration
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

🧪** Jenkins Pipeline**
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
