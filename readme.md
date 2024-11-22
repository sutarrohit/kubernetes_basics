# Kubernetes Deployment for WebApp and MongoDB

This repository contains Kubernetes resource files to deploy a web application and a MongoDB database. The web application connects to MongoDB using secrets and configuration stored in Kubernetes.

## Files Overview

### 1. `mongo-config.yaml`

- **Purpose**: Stores non-sensitive configuration data.
- **Contents**:
  - `mongo-url`: The service name of MongoDB (`mongo-service`), allowing the web app to connect to MongoDB.

---

### 2. `mongo-secret.yaml`

- **Purpose**: Stores sensitive data like MongoDB username and password.
- **Contents**:
  - `mongo-user`: Base64-encoded username (`mongouser`).
  - `mongo-password`: Base64-encoded password (`mongopassword`).
- **Encoding Command**:
  To encode values in Base64:
  ```bash
  echo -n 'mongouser' | base64
  echo -n 'mongopassword' | base64
  ```

---

### 3. `mongo-deployment.yaml`

- **Purpose**: Defines the MongoDB Deployment and Service.
- **Deployment**:
  - Uses the `mongo:5.0` image.
  - Exposes MongoDB on port `27017`.
  - Uses secrets (`mongo-secret.yaml`) to set the root username and password.
- **Service**:
  - Type: `ClusterIP` (internal-only access).
  - Exposes MongoDB at `27017`.

---

### 4. `webapp-deployment.yaml`

- **Purpose**: Defines the WebApp Deployment and Service.
- **Deployment**:
  - Uses the `rohitsutar082/next-demo:latest` image.
  - Connects to MongoDB using:
    - Secrets (`mongo-secret.yaml`) for username and password.
    - ConfigMap (`mongo-config.yaml`) for the MongoDB service URL.
  - Includes the `wait-for-it.sh` script to ensure MongoDB is ready before starting the app.
- **Service**:
  - Type: `NodePort` (external access).
  - Exposes the web app at `NodePort: 30100`.

---

## Commands to Deploy and Run

### 1. Start Minikube

Ensure Minikube is running:

```bash
minikube start
```

### 2. Deploy ConfigMap

Create the ConfigMap for MongoDB URL:

```bash
kubectl apply -f mongo-config.yaml
```

### 3. Deploy Secret

Create the Secret for MongoDB credentials:

```bash
kubectl apply -f mongo-secret.yaml
```

### 4. Deploy MongoDB

Deploy the MongoDB Deployment and Service:

```bash
kubectl apply -f mongo-deployment.yaml
```

Verify that the MongoDB Pod is running:

```bash
kubectl get pods -l app=mongo
```

### 5. Deploy WebApp

Deploy the WebApp Deployment and Service:

```bash
kubectl apply -f webapp-deployment.yaml
```

Verify that the WebApp Pod is running:

```bash
kubectl get pods -l app=webapp
```

### 6. Access the WebApp

Create the Minikube tunnel:

```bash
minikube service webapp-service
```

Access the WebApp in your browser using:

```
http://<tunnel-ip>:tunnel-port
```

### 7. Debugging and Logs

- Check the logs of MongoDB:
  ```bash
  kubectl logs -l app=mongo
  ```
- Check the logs of WebApp:
  ```bash
  kubectl logs -l app=webapp
  ```

---
