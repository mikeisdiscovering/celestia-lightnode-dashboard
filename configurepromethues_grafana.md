## Install promethues

download and extract Prometheus

```
cd
wget https://github.com/prometheus/prometheus/releases/download/v2.30.1/prometheus-2.30.1.linux-amd64.tar.gz && \
tar xvf prometheus-2.30.1.linux-amd64.tar.gz && \
rm prometheus-2.30.1.linux-amd64.tar.gz && \
mv prometheus-2.30.1.linux-amd64 prometheus
```

edit prometheus/prometheus.yml

```
   static_configs:
      - targets: ["localhost:9090"]
## add below
  - job_name: 'celestia'
    static_configs:
      - targets: ['YOURNODEIP:8990'] 
```

Set up service file

```
sudo tee /etc/systemd/system/prometheusd.service > /dev/null <<EOF
[Unit]
Description=prometheus
After=network-online.target
[Service]
User=$USER
ExecStart=$HOME/prometheus/prometheus --config.file="/root/prometheus/prometheus.yml"
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

start promethuesd

```
sudo systemctl daemon-reload && \
sudo systemctl enable prometheusd && \
sudo systemctl restart prometheusd
```

check logs

```
sudo journalctl -u prometheusd -f
```

## Install Grafana using docker 

install docker first

```
curl -fsSL https://get.docker.com -o get-docker.sh 
bash get-docker.sh
```

Run Grafana using docker 

```
sudo docker run -d -p 3033:3000 --name grafana grafana/grafana:9.0.5
```

Open http://yourip:3033 the default username and password is `admin` and `admin`

## Add prometheus data source

In the home page, click  `add your first data source`, choose Prometheus .

Input `http://YourPromethuesIp:9090` Later , click `Save & test`
