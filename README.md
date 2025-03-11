# monitoring
Installation of Prometheus and Grafana on VM machine

Install Prometheus on VM
📌 Step 1: Create a VM
Ensure your VM is running Ubuntu 22.04 or 20.04.
If using GCP/AWS, create a VM with at least:

2 vCPUs
4GB RAM
20GB Disk
📌 Step 2: Update & Install Dependencies
bash
Copy
Edit
sudo apt update && sudo apt install -y wget tar unzip curl
📌 Step 3: Download Prometheus
bash
Copy
Edit
cd /tmp
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-2.51.2.linux-amd64.tar.gz
tar xvf prometheus-*.tar.gz
cd prometheus-2.51.2.linux-amd64
📌 Step 4: Move Binaries to /usr/local/bin
bash
Copy
Edit
sudo mv prometheus /usr/local/bin/
sudo mv promtool /usr/local/bin/
📌 Step 5: Create Prometheus Configuration
bash
Copy
Edit
sudo mkdir -p /etc/prometheus
sudo nano /etc/prometheus/prometheus.yml
📌 Add This Configuration
yaml
Copy
Edit
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
📌 Step 6: Create Systemd Service
bash
Copy
Edit
sudo nano /etc/systemd/system/prometheus.service
📌 Add This Configuration
ini
Copy
Edit
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus
Restart=always

[Install]
WantedBy=multi-user.target
📌 Step 7: Enable & Start Prometheus
bash
Copy
Edit
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
📌 Step 8: Verify Prometheus
bash
Copy
Edit
systemctl status prometheus
Test in browser: http://<VM-IP>:9090

2️⃣ Install Node Exporter for System Metrics
📌 Step 1: Download & Install Node Exporter
bash
Copy
Edit
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.7.0.linux-amd64.tar.gz
tar xvf node_exporter-*.tar.gz
cd node_exporter-1.7.0.linux-amd64
sudo mv node_exporter /usr/local/bin/
📌 Step 2: Create Systemd Service
bash
Copy
Edit
sudo nano /etc/systemd/system/node_exporter.service
📌 Add This Configuration
ini
Copy
Edit
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/bin/node_exporter
Restart=always

[Install]
WantedBy=multi-user.target
📌 Step 3: Enable & Start Node Exporter
bash
Copy
Edit
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
📌 Step 4: Verify Node Exporter
bash
Copy
Edit
systemctl status node_exporter
Test in browser: http://<VM-IP>:9100/metrics

3️⃣ Install Grafana on VM
📌 Step 1: Add Grafana Repository & Install
bash
Copy
Edit
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt-get update
sudo apt-get install -y grafana
📌 Step 2: Enable & Start Grafana
bash
Copy
Edit
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
📌 Step 3: Access Grafana
URL: http://<VM-IP>:3000
Default Login:
Username: admin
Password: admin (change it after logging in)
4️⃣ Configure Prometheus in Grafana
📌 Step 1: Add Prometheus Data Source
Open Grafana → Settings → Data Sources
Select Prometheus
Add URL: http://localhost:9090
Click Save & Test ✅
📌 Step 2: Import Dashboards
Go to Grafana → Dashboards → Import
Use these Dashboard IDs:
Node Exporter Dashboard: 1860
Prometheus Metrics Dashboard: 3662
Click Import ✅
5️⃣ Setup Alerts in Grafana
📌 Step 1: Create Alert in Grafana
Go to Alerting → New Alert Rule
Choose Node Exporter: Memory Usage
Condition: Memory > 85%
Set Notifications (Slack, Email, etc.)
🎯 Final Summary
✅ Prometheus Installed on VM
✅ Node Exporter Installed for System Metrics
✅ Grafana Installed & Configured
✅ Monitoring Dashboards Imported
✅ Alerts Configured for Memory/Disk Usage
