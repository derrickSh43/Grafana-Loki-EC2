![image](https://github.com/user-attachments/assets/39e53100-f9cd-4e76-96e1-ac586eb621db)

# Grafana-Loki-EC2

[![View Project: Grafana and Loki](https://img.shields.io/badge/View%20Project-Grafana%20and%20Loki-brightgreen)](https://github.com/derrickSh43/Grafana-Loki-EC2)
[![GitHub](https://img.shields.io/badge/GitHub-Profile-informational?style=flat&logo=github&logoColor=white&color=blue)](https://github.com/derrickSh43)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Profile-blue?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/derrick-weil-11726253)

---

# **README: Grafana, Loki, and Prometheus on EC2**

This repository provides two distinct projects for deploying observability tools on Amazon Linux 2023 EC2 instances:

1. **Grafana and Loki**: Focuses on log aggregation and visualization.
2. **Grafana, Loki, and Prometheus**: Builds on the first by adding Prometheus for metrics collection and monitoring.

---
## Table of Contents

1. [Projects](#projects)
   
2. [Overview](#overview)
   
   
3. [Shared EC2 Recommendations](#shared-ec2-recommendations)
   
4. [Shared Post-Launch Instructions](#shared-post-launch-instructions)
   
5. [Grafana, Loki, and Prometheus Setup](#grafana-loki-and-prometheus-setup)
   
6. [Dashboards](#dashboards)
   
7. [Troubleshooting](#troubleshooting)
   
8. [Scaling Recommendations](#scaling-recommendations)
   
9. [Conclusion](#conclusion)


## **Projects**

### 1. **Grafana-Loki**  
   - Includes a user data script for setting up **Grafana** and **Loki**.  
   - Focused on centralized log collection and visualization.  
   - See the file: `grafana-loki-setup.yaml`.

### 2. **Grafana-Loki-Prometheus**  
   - Builds on the Grafana-Loki setup by adding **Prometheus**.  
   - Includes metrics monitoring alongside log aggregation.  
   - See the file: `grafana-loki-prometheus-setup.yaml`.  

---

## **Overview**

### **Grafana**
- A dashboard tool for visualizing logs and metrics.

### **Loki**
- A log aggregation system designed to work seamlessly with Grafana.

### **Prometheus** (For the second project)
- A metrics monitoring and alerting toolkit to enhance observability.

---

## **Shared EC2 Recommendations**

### **Instance Type**
- **Grafana-Loki**: Use `t3.medium` for small setups.  
- **Grafana-Loki-Prometheus**: Use `t3.large` or larger for environments that include Prometheus.

### **Storage**
- **Minimum**: 20 GB **EBS** volume.
- **Loki**: Scale storage for logs based on retention needs.
- **Prometheus**: Allocate additional space for metrics storage.

### **Network**
- Security group rules:
  - **Grafana**: Port `3000` (HTTP).
  - **Loki**: Port `3100` (log ingestion).
  - **Prometheus** (second project): Port `9090` (metrics endpoint).
  - Restrict access by IP for security.

---

## **Shared Post-Launch Instructions**

### **Access Grafana**
1. Open your browser and navigate to:
   ```
   http://<EC2-Public-IP>:3000
   ```
2. Log in with:
   - **Username**: `admin`
   - **Password**: `admin` (you’ll be prompted to change this).

### **Add Loki as a Data Source**
- Refer to the steps in the **Grafana-Loki** project to configure Loki as a Grafana data source.

---

## **Grafana, Loki, and Prometheus Setup**

For the **Grafana-Loki-Prometheus** project:

### Add Prometheus as a Data Source
1. Open Grafana and navigate to **Connections > Data Sources**.
2. Select **Prometheus** and configure:
   - URL: `http://localhost:9090`.
3. Save and test the connection.

### Prometheus Configuration
Prometheus configuration is located in `/etc/prometheus/prometheus.yml`. Add scrape targets to monitor additional services, such as:

```yaml
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
  - job_name: 'application_metrics'
    static_configs:
      - targets: ['localhost:8080']
```

Restart Prometheus after updating the configuration:
```bash
sudo systemctl restart prometheus
```

---

## **Getting Started with Grafana Dashboards**

### **1. Import Pre-Built Dashboards**
- Grafana has pre-built dashboards for Loki:
  1. Go to **Create > Import**.
  2. Use dashboard ID 13685 (Loki dashboard) or similar.

### **2. Create a Custom Dashboard**
1. Go to **Create > Dashboard**.
2. Add a new panel.
3. Query Loki for logs:
   
```bash
   {job="webserver"}
```
4. Save the dashboard for easy access.

### **3. For Prometheus**
Use dashboards tailored to node metrics or application metrics.

---

## **Troubleshooting**

### **1. Logs Not Showing in Grafana**
- Check if Loki is running:

```bash
  sudo systemctl status loki
```
- Verify the Promtail configuration on source instances:
  
```bash
  sudo journalctl -u promtail
```
- Ensure the Loki server URL in Promtail matches the actual Loki instance.

### **2. Grafana Not Accessible**
- Confirm Grafana is running:
  
```bash
  sudo systemctl status grafana-server
```
- Check the firewall or security group for port 3000.

### **3. Logs Not Ingested by Loki**
- Inspect Loki logs for errors:
  
```bash
  sudo journalctl -u loki
```
### Metrics Not Showing in Grafana (Grafana-Loki-Prometheus)
- Verify Prometheus is running:
  ```bash
  sudo systemctl status prometheus
  ```
- Confirm scrape targets are configured correctly in `prometheus.yml`.
---

## **Scaling Recommendations**

1. **Separate Grafana and Loki**:
   - For larger environments, deploy Grafana and Loki on separate instances.
2. **Use S3 for Loki Storage**:
   - Configure S3 as the backend for Loki to handle large volumes of logs.
3. **Clustered Setup**:
   - Deploy multiple Loki instances with a load balancer for redundancy and scalability. ---- I have this but have two files here one with just Grafana and loki which this covers and one that has those and adds Prometheus , can you update the read me so it reflects that there are two file and prometheus part
