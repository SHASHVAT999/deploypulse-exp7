# Experiment 7 - CI/CD using Jenkins, Docker and Kubernetes

Application: DeployPulse Dashboard

## Technology Stack
- Git: Version control
- Jenkins: CI/CD automation
- Docker: Containerization
- Kubernetes: Container orchestration
- Nginx: Web server

## Deployment
The Jenkins pipeline checks source control, builds a Docker image,
loads it into the local kind cluster and deploys two Kubernetes replicas.

## Access
Application: http://localhost:8085
