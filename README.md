![image](https://github.com/user-attachments/assets/39e53100-f9cd-4e76-96e1-ac586eb621db)

# Grafana-Loki-EC2

[![View Project: Grafana and Loki](https://img.shields.io/badge/View%20Project-Grafana%20and%20Loki-brightgreen)](https://github.com/derrickSh43/Grafana-Loki-EC2)
[![View Project: Grafana, Loki, and Prometheus](https://img.shields.io/badge/View%20Project-Grafana%2C%20Loki%2C%20Prometheus-blue)](https://github.com/derrickSh43/Grafana-Loki-Prometheus-EC2)
[![GitHub](https://img.shields.io/badge/GitHub-Profile-informational?style=flat&logo=github&logoColor=white&color=blue)](https://github.com/derrickSh43)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Profile-blue?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/derrick-weil-11726253)

---

# **README: Grafana, Loki, and Prometheus on EC2**

This repository provides two distinct projects for deploying observability tools on Amazon Linux 2023 EC2 instances:

1. **Grafana and Loki**: Focuses on log aggregation and visualization.
2. **Grafana, Loki, and Prometheus**: Builds on the first by adding Prometheus for metrics collection and monitoring.

---

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

## **Dashboards**

### For Both Projects
- **Loki Dashboard**: Import pre-built dashboard ID `13685` to visualize logs.

### For Grafana-Loki-Prometheus
- **Prometheus Dashboard**: Use pre-built dashboards tailored for Prometheus metrics.

---

## **Troubleshooting**

### Logs Not Showing in Grafana
- Check Loki and Promtail statuses:
  ```bash
  sudo systemctl status loki
  sudo systemctl status promtail
  ```

### Metrics Not Showing in Grafana (Grafana-Loki-Prometheus)
- Verify Prometheus is running:
  ```bash
  sudo systemctl status prometheus
  ```
- Confirm scrape targets are configured correctly in `prometheus.yml`.

---

## **Scaling Recommendations**

### For Both Projects
1. **Separate Services**: Deploy Grafana, Loki, and Prometheus on separate instances for scalability.
2. **Loki Storage**: Use S3 as the backend for Loki to handle high log volumes.

### For Grafana-Loki-Prometheus
1. **Prometheus High Availability**: Set up clustering or federation for larger environments.
2. **Storage Scaling**: Allocate additional storage for metrics retention.

---

## **Conclusion**

These projects provide scalable solutions for observability using Grafana, Loki, and Prometheus on EC2 instances. Start with the Grafana-Loki setup for log aggregation and visualization, and expand to the Grafana-Loki-Prometheus project for full observability with metrics monitoring.

Explore the configurations and adapt them to your specific environment.
```
