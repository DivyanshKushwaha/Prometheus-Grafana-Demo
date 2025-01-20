# Observability in Microservices

## Why Observability Matters
- Microservices are inherently distributed, making it difficult to track system behavior.
- Observability provides insights into the state and health of the system through **logs**, **metrics**, and **traces**.
- It helps in identifying bottlenecks, detecting failures, and ensuring reliability and performance.

---

## Monitoring Basics

### Definition
Monitoring is the process of collecting and visualizing data about a system’s health and performance over time.

### Key Questions Monitoring Answers:
- Is the service running?
- Is the service functioning as expected?
- Is the service performing within acceptable thresholds?

### Monitoring vs. Observability
| **Monitoring**                  | **Observability**                   |
|----------------------------------|--------------------------------------|
| Tracks predefined metrics.       | Offers deeper insights into unknown issues. |
| Answers "What is wrong?"         | Answers "Why is it wrong?"           |
| Focused on system health.        | Focused on understanding internal state.     |

---

## Telemetry Data

### Types of Telemetry Data:
1. **Metrics**: Quantitative data, e.g., CPU usage, response time.
2. **Logs**: Detailed event records.
3. **Traces**: Sequence of events showing request flow.

---

## Methods of Metrics Collection

### Push Method
- **How it works**: Clients send data to a centralized system periodically.
- **Example**: Using Prometheus Pushgateway for short-lived jobs.
- **Pros**: Works well for dynamic, short-lived services.
- **Cons**: Higher client responsibility for sending data.

### Scrape Method
- **How it works**: Centralized system (e.g., Prometheus) pulls data from endpoints.
- **Example**: Prometheus scrapes metrics from `/metrics` exposed by an exporter.
- **Pros**: Decouples data collection from clients.
- **Cons**: Services must expose data at specific endpoints.

---

## Exporter and Pushgateway

### Exporter
- A tool that exposes application or system metrics in a Prometheus-compatible format.
- **Example**: Node Exporter collects system-level metrics like CPU and memory usage.

### Pushgateway
- Allows ephemeral jobs to push metrics directly to Prometheus.
- Used when a service doesn't persist long enough for Prometheus to scrape.

---

## Prometheus Data Model
- Prometheus stores time series data.

### Each time series is identified by:
1. **Metric name**: Represents what is being measured (e.g., `http_requests_total`).
2. **Labels**: Key-value pairs for filtering and grouping (e.g., `status="200"`).

### Example:
```plaintext
predict_api_hit{count="1", time_taken="600"}
```

---

## PromQL Basics
PromQL is the query language for Prometheus.

### Examples:
- `up`: Shows whether targets are up (1) or down (0).
- `rate(http_requests_total[5m])`: Computes request rate over the last 5 minutes.
- `sum by (status)(http_requests_total)`: Groups and sums requests by HTTP status.

---

## Grafana Cloud

### Advantages:
- No need for setup or maintenance.
- Automatically updated with the latest features.
- Handles complex architectures out-of-the-box.

### Disadvantages:
- Higher costs for scaling.
- Dependency on the vendor.
- Reduced control over security and data management.

---

## On-Premise Grafana Setup

### Advantages:
- Complete control over data and security.
- Tailored to specific organizational needs.
- No recurring license costs for basic features.

### Disadvantages:
- Requires infrastructure and maintenance.
- Scalability can be challenging.
- Manual updates and configurations.

---

## Alerting in Grafana

### How Alerts Work:
- Alerts are raised when specific conditions (rules) are met.
- Rules are defined using queries (e.g., `CPU usage > 80%`).

### Components:
1. **Alert Manager**: Evaluates rules and triggers alerts.
2. **Notification Policies**: Define how alerts are routed.
3. **Contact Points**: Email, Slack, PagerDuty, or other mediums for notifications.

### Setup Example:
1. Define a query:
   ```plaintext
   avg_over_time(cpu_usage[5m]) > 80
   ```
2. Configure a notification policy to send alerts to an email address.

---

## Changes to Be Made on `default.ini` File
To add your email into the Grafana alert notification system:

```ini
[smtp]
enabled = true
host = smtp.gmail.com:587
user = your-email@gmail.com
password = your-generated-app-password
from_address = your-email@gmail.com
from_name = Grafana Alerts
skip_verify = true
