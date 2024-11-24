# Grafana-Loki-EC2
Grafana-Loki-EC2

---

# **README: Grafana and Loki on EC2**

This guide provides instructions for managing Grafana and Loki installed via a user data script on Amazon Linux 2023 EC2 instances.

---

## **Overview**

- **Grafana**: A powerful dashboarding tool for visualizing logs and metrics.
- **Loki**: A lightweight log aggregation tool that works seamlessly with Grafana.
- **Purpose**: Centralized log collection and visualization for EC2 instances.

---

## **EC2 Recommendations**

### **Instance Type**
- **Minimum**: `t3.medium` (2 vCPU, 4 GB RAM)
  - Suitable for environments with a few dozen log sources.
- **Recommended**: `t3.large` (2 vCPU, 8 GB RAM)
  - Handles larger environments with multiple log sources and dashboards.

### **Storage**
- Use an **EBS** volume with at least 20 GB.
  - Scale based on your log retention needs if you're using local storage for Loki.

### **Network**
- Security group rules:
  - **Grafana**: Allow inbound traffic on port `3000` (HTTP).
  - **Loki**: Allow inbound traffic on port `3100` (log ingestion).
  - Restrict access by source IP to enhance security.

---

## **Post-Launch Instructions**

### **1. Access Grafana**
- Open your browser and navigate to:
  ```
  http://<EC2-Public-IP>:3000
  ```
- Log in with:
  - **Username**: `admin`
  - **Password**: `admin` (you’ll be prompted to change this on first login).

### **2. Add Loki as a Data Source**
1. Go to **Connections > Data Sources**.
2. Click **Add data source** and select **Loki**.
3. Configure the data source:
   - URL: `http://localhost:3100` (or replace `localhost` with the private IP if Loki is on another instance).
4. Click **Save & Test** to verify the connection.

### **3. Verify Logs**
- Navigate to **Explore** (compass icon) in Grafana.
- Select the Loki data source and run a query, such as:
  ```bash
  {job="system"}
  ```
- Logs should appear in the results.

---

## **Configuration Changes**

### **Grafana**
- Grafana configuration is stored in `/etc/grafana/grafana.ini`.
- Common changes:
  1. **Customizing ports**:
     ```ini
     [server]
     http_port = 8080
     ```
  2. **Enabling SSL**:
     Set `cert_file` and `cert_key` paths under `[server]`.

   After any changes:
   ```bash
   sudo systemctl restart grafana-server
   ```

### **Loki**
- Loki configuration is stored in `/etc/loki/loki-config.yaml`.
- Common changes:
  1. **Retention Period**:
     ```yaml
     table_manager:
       retention_period: 168h
     ```
  2. **Changing storage backend**:
     To use Amazon S3:
     ```yaml
     storage_config:
       aws:
         s3: s3://<bucket-name>
         region: us-east-1
     ```
     Replace `<bucket-name>` with your S3 bucket.

   After any changes:
   ```bash
   sudo systemctl restart loki
   ```

### **Promtail (on source EC2 instances)**
- Promtail configuration is stored in `/etc/promtail/promtail-config.yaml`.
- To add or modify log paths:
  ```yaml
  scrape_configs:
    - job_name: custom_logs
      static_configs:
        - targets:
            - localhost
          labels:
            job: custom
            __path__: /path/to/your/logs/*.log
  ```

   Restart Promtail:
   ```bash
   sudo systemctl restart promtail
   ```

---

## **Getting Started with Grafana Dashboards**

### **1. Import Pre-Built Dashboards**
- Grafana has pre-built dashboards for Loki:
  1. Go to **Create > Import**.
  2. Use dashboard ID `13685` (Loki dashboard) or similar.

### **2. Create a Custom Dashboard**
1. Go to **Create > Dashboard**.
2. Add a new panel.
3. Query Loki for logs:
   ```bash
   {job="webserver"}
   ```
4. Save the dashboard for easy access.

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
- Check the firewall or security group for port `3000`.

### **3. Logs Not Ingested by Loki**
- Inspect Loki logs for errors:
  ```bash
  sudo journalctl -u loki
  ```

---

## **Scaling Recommendations**

1. **Separate Grafana and Loki**:
   - For larger environments, deploy Grafana and Loki on separate instances.
2. **Use S3 for Loki Storage**:
   - Configure S3 as the backend for Loki to handle large volumes of logs.
3. **Clustered Setup**:
   - Deploy multiple Loki instances with a load balancer for redundancy and scalability.

