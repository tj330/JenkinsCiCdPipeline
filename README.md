# Jenkins CI/CD Pipeline for Spring Boot Application

This repository demonstrates how I implemented a robust CI/CD pipeline for a Spring Boot application using Jenkins, Docker, SonarQube, and Kubernetes. Below, I explain the process, structure, and technologies used to achieve automated building, testing, code analysis, containerization, and deployment.

---

## Project Overview

- **Spring Boot App:** A simple Java web application (see [`spring-boot-app/`](spring-boot-app/)) built with Maven.
- **CI/CD Pipeline:** All automation is managed by a Jenkins Pipeline defined as code ([`JenkinsFile`](spring-boot-app/JenkinsFile)).
- **Quality Gates:** Integrated SonarQube for static code analysis.
- **Containerization:** The application is built into a Docker image.
- **Automated Deployment:** Image version updates are pushed to a Kubernetes deployment manifest.

---

## CI/CD Workflow

### 1. Jenkins Pipeline as Code

The pipeline is defined in [`spring-boot-app/JenkinsFile`](spring-boot-app/JenkinsFile):

- **Docker-based Agent:** Builds run inside a Docker container with Maven and Docker CLI for consistency.
- **Stages:**
  1. **Checkout:** Pulls the latest source code from the main branch.
  2. **Build & Test:** Uses Maven to build the Spring Boot app and run tests.
  3. **Static Code Analysis:** Runs SonarQube analysis to enforce code quality standards.
  4. **Build & Push Docker Image:** Builds a Docker image and pushes it to Docker Hub (`tj330/ultimate-cicd`).
  5. **Update Deployment Manifest:** Updates the Kubernetes deployment YAML with the new image tag and pushes the change to GitHub.

> **Note:** Credentials for Docker Hub, GitHub, and SonarQube are managed securely in Jenkins.

---

### 2. Spring Boot Application

- Main entry point: [`StartApplication.java`](spring-boot-app/src/main/java/com/tj330/StartApplication.java)
- Uses Spring Boot with Thymeleaf for rendering a simple web UI.
- Maven configuration: [`pom.xml`](spring-boot-app/pom.xml)

---

### 3. Static Code Analysis

- The pipeline includes a stage for SonarQube analysis.
- SonarQube server URL and token are provided via Jenkins credentials.
- Ensures code meets quality standards before moving to image build and deployment.

---

### 4. Dockerization

- The `docker build` command creates a container image of the Spring Boot app.
- The image is tagged using the Jenkins build number for traceability.
- The built image is pushed to Docker Hub using Jenkins credentials.

---

### 5. Kubernetes Deployment

- The pipeline updates the Docker image tag in [`spring-boot-app-manifests/deployment.yml`](spring-boot-app-manifests/deployment.yml) after each successful build.
- The updated manifest is committed and pushed back to the repository, ensuring versioned and auditable deployments.
- This manifest is ready to be used by a GitOps tool like ArgoCD for automatic rollout to your Kubernetes cluster.

---

## How to Run This Project

1. **Clone the Repository**
   ```bash
   git clone https://github.com/tj330/JenkinsCiCdPipeline.git
   cd JenkinsCiCdPipeline
   ```

2. **Configure Jenkins:**
   - Install Jenkins with Docker, Git, Pipeline, and Blue Ocean plugins.
   - Set up credentials for Docker Hub, SonarQube, and GitHub.

3. **Create a Jenkins Pipeline Job:**
   - Use the provided `spring-boot-app/JenkinsFile`.
   - Set the repository URL to your fork or this repo.

4. **Set up SonarQube (Optional but recommended):**
   - Run a SonarQube server and configure the token in Jenkins.

5. **Run the Pipeline:**  
   - Trigger the pipeline via Jenkins UI or push a commit.

6. **Deploy to Kubernetes:**  
   - Ensure your Kubernetes cluster and GitOps (like ArgoCD) are set up to watch for manifest changes.

---

## Why This Approach?

- **End-to-End Automation:** Every code change is built, tested, scanned, containerized, and deployed automatically.
- **Immutable Deployments:** Each image is versioned and traceable back to a specific build.
- **Security & Quality:** Secrets are managed in Jenkins; code quality is enforced via SonarQube.
- **GitOps Ready:** Deployment manifests are managed as code for full traceability.

---

## Credits

- Inspired by modern DevOps best practices and CI/CD courses.
