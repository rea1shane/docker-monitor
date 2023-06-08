# Docker monitor

Prometheus-based monitoring system. The following components are included:

- [Prometheus](https://github.com/prometheus/prometheus)
- [Pushgateway](https://github.com/prometheus/pushgateway)
- [Blackbox exporter](https://github.com/prometheus/blackbox_exporter)
- [Grafana](https://github.com/grafana/grafana)
- [Alertmanager](https://github.com/prometheus/alertmanager)
- [karma](https://github.com/prymitive/karma)

## Usage

### Preparation

1. Download template configuration file:

   ```shell
   # Make workspace
   mkdir -p ~/tmp/monitor/prometheus/alertmanager/
   mkdir -p ~/tmp/monitor/prometheus/blackbox_exporter/

   # Download template configuration file
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O ~/tmp/monitor/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O ~/tmp/monitor/prometheus/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O ~/tmp/monitor/prometheus/blackbox_exporter/blackbox.yml
   ```

2. Edit `~/tmp/monitor/prometheus/prometheus.yml`：

   ```yaml
   alerting:
     alertmanagers:
       - static_configs:
           - targets:
               # Specify Alertmanager
               - alertmanager:9093

   scrape_configs:
     # Monitoring Pushgateway
     - job_name: "pushgateway"
       static_configs:
         - targets: ["pushgateway:9091"]

     # Monitoring Blackbox exporter
     - job_name: "blackbox_exporter"
       static_configs:
         - targets: ["blackbox_exporter:9115"]
   ```

#### Add Prometheus alert rules (Optional)

1. Add alert rules. For example, using the alert rules in the [monitor](https://github.com/rea1shane/monitor) repository:

   ```shell
   git clone https://github.com/rea1shane/monitor.git ~/tmp/monitor/resources
   ```

2. Edit `~/tmp/monitor/prometheus/prometheus.yml`, make Prometheus load alert rules:

   ```yaml
   rule_files:
     - /etc/prometheus/rules/*.yml
     - /etc/prometheus/rules/*.yaml
   ```

#### Custom configuration files (Optional)

Edit following files if you like:

```
~/tmp/monitor/prometheus/prometheus.yml
~/tmp/monitor/prometheus/alertmanager/alertmanager.yml
~/tmp/monitor/prometheus/blackbox_exporter/blackbox.yml
```

### Start servers

Just run:

```shell
docker-compose up
```

Components port list:

- Prometheus: `9090`
- Pushgateway: `9091`
- Blackbox exporter: `9115`
- Grafana: `3000`
- Alertmanager: `9093`
- karma: `8080`
