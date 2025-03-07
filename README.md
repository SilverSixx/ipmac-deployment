# IPMAC Deployment Guide

## Overview

This repository contains Kubernetes configuration files for deploying a microservices-based application stack on **Minikube**. The setup includes backend services, a frontend, Kafka, Keycloak for authentication, Nexus as an artifact repository, and a PostgreSQL database.

## Directory Structure

```plaintext
silverSixx-IPMAC-Deployment/
├── README.md               # This file
├── backend/
│   ├── admin-service.yml   # Admin service deployment
│   ├── common-service.yml  # Common service deployment
│   └── training-service.yml# Training service deployment
├── common/
│   ├── namespace.yml       # Kubernetes namespace configuration
│   └── storage-class.yml   # Storage class definition
├── db/
│   └── deployment.yml      # PostgreSQL database deployment
├── frontend/
│   └── deployment.yml      # Frontend (React/Angular) deployment
├── kafka/
│   ├── kafka-deployment.yml# Apache Kafka cluster deployment
│   └── kafka-ui-deployment.yml # Kafka UI deployment
├── keycloak/
│   ├── deployment.yml      # Keycloak authentication server
│   └── service.yml         # Keycloak service exposure
└── nexus/
    ├── deployment.yml      # Nexus repository manager deployment
    └── storage.yml         # Persistent storage for Nexus
```

## Implemented Features

- Backend Services: Admin, Common, and Training services (Spring Boot/Jersey)
- Frontend: Vuejs baseed UI
- Authentication: Keycloak for OAuth2/OpenID Connect
- Artifact Management: Nexus OSS for Docker/Maven/npm artifacts
- Event Streaming: Apache Kafka with UI monitoring
- Database: PostgreSQL for data storage
- Local Kubernetes: Minikube for local development/testing

## Technologies Used

### Backend

- Java 17
- Spring Boot
- PostgreSQL

### Frontend

- VueJS + Tailwind + Shadcn/ui components
- Axios
- OAuth2Client using PKCE (Keycloak integration)

### Infrastructure

- Kubernetes (Minikube)
- Docker
- Apache Kafka
- Keycloak
- Nexus Repository OSS

### DevOps

- Helm (for Kafka)
- kubectl
- Minikube

## Prerequisites

- Install Docker 
- Install Minikube
- Install kubectl
- Install Helm (for Kafka)

## Deployment Instructions

1. **Start minikube**
```bash
minikube start --driver=docker --memory=6144  # give minikube at least 6G
minikube addons enable ingress  # Enable ingress controller
```

2. **Deploy common resources and services**

```bash
kubectl apply -f common/namespace.yml
kubectl apply -f common/storage-class.yml

kubectl apply -f nexus/

kubectl apply -f db/

# deploy kafka with its operator - strimzi
kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka

kubectl apply -f kafka/kafka-deployment.yml
# wait a bit for the kafka to up
kubectl apply -f kafka/kafka-ui-deployment.yml

kubectl apply -f backend/

kubectl apply -f frontend/
```

3. **Access the application**

add the ip to hosts
```sh
echo "$(minikube ip) app.ipmac.dat" | sudo tee -a /etc/hosts

# further info please refer to: https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

```
then run
```sh
minikube tunnel
```

After that you can reach for the app through http://app.ipmac.dat from the browser.