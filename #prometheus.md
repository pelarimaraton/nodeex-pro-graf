#prometheus
==============================================================================================================
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
chmod u+x *
tar xvf prometheus-2.52.0.linux-amd64.tar.gz
groupadd --system prometheus
useradd --system -s /sbin/nologin -g prometheus prometheus
cd prometheus-2.52.0.linux-amd64
mv prometheus promtool /usr/local/bin/
which prometheus
which promtool
prometheus --version
mkdir /etc/prometheus
mkdir /var/lib/prometheus
chown -R prometheus: /var/lib/prometheus/
mv consoles/ console_libraries/ prometheus.yml /etc/prometheus/
cd /etc/prometheus/

=============================================================================================================
vi prometheus.yml

global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.


scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node-exporter"
    static_configs:
      - targets: ["localhost:9100"]

  - job_name: "vm01"
    static_configs:
      - targets: ["10.10.10.10:9100"]
=============================================================================================================

=============================================================================================================
vi /etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
=============================================================================================================

systemctl daemon-reload
systemctl status prometheus
systemctl enable --now prometheus
systemctl status prometheus
ss -tulpn | grep LISTEN






