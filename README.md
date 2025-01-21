# Flask Application with Metrics

🚀 A complete guide to deploying a Flask application on Minikube, setting up Prometheus for metrics collection, and visualizing data on Grafana. This step-by-step tutorial is beginner-friendly and packed with practical instructions for a seamless hands-on experience.

## Project Overview

This repository demonstrates:

- How to deploy a Flask application locally and on Minikube.
- Setting up Prometheus to scrape metrics from the Flask app.
- Integrating Prometheus with Grafana for monitoring and visualization.

---

## Table of Contents

1. [Getting Started with Flask App](#getting-started-with-flask-app)
2. [Deploying Flask App on Minikube](#deploying-flask-app-on-minikube)
3. [Setting Up Prometheus](#setting-up-prometheus)
4. [Configuring Prometheus to Scrape Flask Metrics](#configuring-prometheus-to-scrape-flask-metrics)
5. [Installing Grafana](#installing-grafana)
6. [Visualizing Metrics with Grafana Dashboards](#visualizing-metrics-with-grafana-dashboards)

---

## Getting Started with Flask App

1. Prepare the Flask app locally.
2. Ensure the app includes metrics endpoints for Prometheus to scrape.

---

## Deploying Flask App on Minikube

### Build Docker Image:
```bash
docker build -t flask-metrics-app:latest .
```

### Start Minikube:
```bash
minikube start
```

### Load Docker Image into Minikube Cluster:
```bash
minikube image load flask-metrics-app:latest
```

### Deploy Flask App:
- Create a `flask-app.yaml` Kubernetes manifest file.
- Apply the manifest:
  ```bash
  kubectl apply -f flask-app.yaml
  ```

### Access the Flask App:
- Get the service URL:
  ```bash
  minikube service flask-metrics-app --url
  ```

---

## Setting Up Prometheus

### Install Helm:

#### Windows:
```bash
winget install Helm.Helm
```

#### macOS:
```bash
brew install helm
```

### Add Prometheus Helm Chart:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### Install Prometheus:
```bash
helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```

---

## Configuring Prometheus to Scrape Flask Metrics

### Retrieve Flask App Service IP:
```bash
kubectl get svc
```
- Note the `ClusterIP` and port for `flask-metrics-app`.

### Edit Prometheus ConfigMap:
Add your Flask app as a scrape target:
```yaml
scrape_configs:
  - job_name: 'flask-app'
    static_configs:
      - targets: ['<FLASK-APP-IP>:5000']
```

### Restart Prometheus:
```bash
kubectl rollout restart deployment prometheus-server -n monitoring
```

### Verify Metrics Collection:
Access the Prometheus dashboard:
```bash
kubectl port-forward -n monitoring svc/prometheus-server 9090:80
```

---

## Installing Grafana

### Add Grafana Helm Chart:
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### Install Grafana:
```bash
helm install grafana grafana/grafana -n monitoring --create-namespace
```

### Access Grafana Locally:
Port-forward Grafana to your local machine:
```bash
kubectl port-forward svc/grafana -n monitoring 3000:80
```

### Log in with the Default Credentials:
- **Username:** `admin`
- **Password:** Obtain using:
  ```bash
  kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode
  ```

---

## Visualizing Metrics with Grafana Dashboards

### Add Prometheus as a Data Source:
1. Navigate to: **Connections > Data Sources > Add Data Source > Prometheus**
2. Use Prometheus URL:
   ```
   http://prometheus-server.monitoring.svc.cluster.local:80
   ```
3. Click **Save & Test**.

### Create a Dashboard:
1. Navigate to: **Dashboards > New Dashboard > Add New Panel**.
2. Visualize metrics such as `total_api_requests_total`.

---

## Highlights

- **End-to-End Observability:** Full setup for monitoring a Flask app with Prometheus and Grafana.
- **Production-Ready Notes:** Includes steps for Minikube and considerations for scaling to production.
- **Beginner-Friendly Guide:** Clear instructions to help even first-time users.
