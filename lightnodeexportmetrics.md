# lightnode-export-metrics

## Change your service file

Assume you are using SystemD to run your light node.change your service file like below.

```
[Unit]
Description=celestia-lightd Light Node
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/bin/celestia light start --core.ip https://rpc-blockspacerace.pops.one --core.rpc.port 26657 --core.grpc.port 9090 --keyring.accname my_celes_key --gateway --gateway.addr 127.0.0.1 --gateway.port 26659  --metrics.tls=false --metrics --metrics.endpoint localhost:4318 --p2p.network blockspacerace
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

Actually we add `--metrics --metrics.endpoint localhost:4318` to export metrics from celestia light node. This will export the metrics to 4318.

## Use node_exporter to collect metrics from machine

install node_exporter

```
cd $HOME
sudo  apt-get update &&  sudo  apt-get upgrade -y && \

wget https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz && \

tar xvf node_exporter-1.2.2.linux-amd64.tar.gz && \

rm node_exporter-1.2.2.linux-amd64.tar.gz && \

sudo mv node_exporter-1.2.2.linux-amd64 node_exporter && \

chmod +x  $HOME/node_exporter/node_exporter && \

mv $HOME/node_exporter/node_exporter /usr/bin && \

rm -Rvf  $HOME/node_exporter/
```

Set up service file

```
sudo tee /etc/systemd/system/exporterd.service > /dev/null <<EOF
[Unit]
Description=node_exporter
After=network-online.target
[Service]
User=$USER
ExecStart=/usr/bin/node_exporter
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

start node exporter

```
sudo systemctl daemon-reload && \
sudo systemctl enable exporterd && \
sudo systemctl restart exporterd
```

check logs

```
sudo journalctl -u exporterd -f
```

## Use opentelemetry-collector to collect metrics and export to promotheus

install `opentelemetry-collector ` 

```
sudo apt-get update
sudo apt-get -y install wget systemctl
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.75.0/otelcol_0.75.0_linux_amd64.deb
sudo dpkg -i otelcol_0.75.0_linux_amd64.deb
```

edit `/etc/otelcol/config.yaml `

Below is some config that we need edite.

```
  # Collect own metrics
  prometheus:
    config:
      scrape_configs:
      - job_name: 'otel-collector'
        scrape_interval: 10s
        static_configs:
        - targets: ['0.0.0.0:8888']
      - job_name: 'node_exporter'
        scrape_interval: 10s
        static_configs:
        - targets: ['localhost:9100'] 
```



```
exporters:
  logging:
    verbosity: detailed
  prometheus:
    endpoint: "0.0.0.0:8990"
    send_timestamps: true
    metric_expiration: 180m

service:

  pipelines:

    traces:
      receivers: [otlp, opencensus, jaeger, zipkin]
      processors: [batch]
      exporters: [logging]

    metrics:
      receivers: [otlp, opencensus, prometheus]
      processors: [batch]
      exporters: [logging,prometheus]

  extensions: [health_check, pprof, zpages]
```

after editing the config, we can restart the otelcol 

```
sudo systemctl restart otelcol
```

We can open http://yourip:8990/metrics  The metrics should be displayed on this page.