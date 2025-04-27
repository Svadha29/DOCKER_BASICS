# Microservices Orchestration with Minikube and Kubernetes

This guide walks through the process of setting up and deploying microservices using Minikube and Kubernetes on your local machine. It demonstrates deploying a **backend service** and an **API Gateway** with Docker containers managed by Kubernetes.

## Prerequisites

- **Docker Desktop**: Install Docker Desktop from [Docker](https://www.docker.com/products/docker-desktop).
- **Minikube**: Install Minikube from [Minikube](https://minikube.sigs.k8s.io/docs/).
- **kubectl**: Install kubectl from [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

Ensure you have Docker Desktop, Minikube, and kubectl installed and running on your system.

## 🚀 Setup Instructions

### 1️⃣ Install Required Tools

- [Install Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Install Minikube](https://minikube.sigs.k8s.io/docs/)
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

### 2️⃣ Start Minikube

Start Minikube with Docker as the driver:

```bash
minikube start --driver=docker
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9440b8c5b031c3a8a14bbea65317c22bd417b2d5/11.Microservices%20Orchestration%20with%20Minikube%20and%20Kubernetes/images/image%20copy%202.png)

This command initializes a Kubernetes cluster within Docker containers.

### 3️⃣ Set Up Docker Environment

To configure your local Docker environment to interact with Minikube's Docker daemon, run the following command:

```bash
eval $(minikube -p minikube docker-env)
```

### 4️⃣ Build and Deploy Microservices

Navigate to your project directory and deploy the **backend** and **API Gateway** services.

#### 🔹 Backend Service

1. Navigate to the **backend** service directory:

    ```bash
    cd backend
    ```

2. Build the Docker image for the backend:

    ```bash
    docker build -t backend-service .
    ```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/efd88de86b8b671556797a0e67c62c2e04a725c0/11.Microservices%20Orchestration%20with%20Minikube%20and%20Kubernetes/images/image.png)
3. Apply the Kubernetes deployment for the backend service:

    ```bash
    kubectl apply -f ../kubernetes/backend-service.yaml
    ```

#### 🔹 API Gateway Service

1. Navigate to the **API Gateway** directory:

    ```bash
    cd ../api-gateway
    ```

2. Build the Docker image for the API Gateway:

    ```bash
    docker build -t api-gateway .
    ```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9440b8c5b031c3a8a14bbea65317c22bd417b2d5/11.Microservices%20Orchestration%20with%20Minikube%20and%20Kubernetes/images/image%20copy%203.png)

3. Apply the Kubernetes deployment for the API Gateway:

    ```bash
    kubectl apply -f ../kubernetes/api-gateway.yaml
    ```

### 5️⃣ Verify Deployment

To check the status of your services and deployments:

```bash
kubectl get deployments
kubectl get services
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/9440b8c5b031c3a8a14bbea65317c22bd417b2d5/11.Microservices%20Orchestration%20with%20Minikube%20and%20Kubernetes/images/image%20copy%204.png)

### 6️⃣ Expose and Access the Application

Expose the **API Gateway** service if it's not already done:

```bash
kubectl expose deployment api-gateway --type=NodePort --port=80
```

To access the **API Gateway** service, use the following command:

```bash
minikube service api-gateway --url
```
![img](https://github.com/Svadha29/DOCKER_BASICS/blob/efd88de86b8b671556797a0e67c62c2e04a725c0/11.Microservices%20Orchestration%20with%20Minikube%20and%20Kubernetes/images/image%20copy.png)

This command will provide a URL where you can access the API Gateway in your browser.

### 7️⃣ Test the Application

You should see responses from both the backend and the API Gateway. If everything is working correctly, the API Gateway will relay messages to the backend.

### 8️⃣ Cleanup

To delete the services and deployments after you’re done:

```bash
kubectl delete service api-gateway
kubectl delete deployment api-gateway
kubectl delete service backend-service
kubectl delete deployment backend-service
```

Stop Minikube:

```bash
minikube stop
```

### 🎯 Conclusion

You’ve successfully set up a Kubernetes-based microservices architecture using Minikube and Docker! This setup includes backend and API Gateway services orchestrated through Kubernetes, providing a robust environment for local development and testing.

---

## Project Structure

```
.
├── api-gateway
│   ├── Dockerfile
│   └── ...
├── backend
│   ├── Dockerfile
│   └── ...
├── kubernetes
│   ├── backend-service.yaml
│   ├── api-gateway.yaml
│   └── ...
├── README.md
└── ...
```
