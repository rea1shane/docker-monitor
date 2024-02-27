# Monitor

Prometheus-based monitoring system. The following components are included:

- [Prometheus](https://github.com/prometheus/prometheus)
- [Pushgateway](https://github.com/prometheus/pushgateway)
- [Blackbox exporter](https://github.com/prometheus/blackbox_exporter)
- [Grafana](https://github.com/grafana/grafana)
- [Alertmanager](https://github.com/prometheus/alertmanager)
- [karma](https://github.com/prymitive/karma)

## Usage

### Preparation

1. Create workspace:

   ```shell
   # First, specify a path
   # e.g.
   export MONITOR_WORKSPACE=~/tmp/monitor

   # Then, create dirs
   mkdir -p $MONITOR_WORKSPACE/prometheus/
   mkdir -p $MONITOR_WORKSPACE/alertmanager/
   mkdir -p $MONITOR_WORKSPACE/blackbox_exporter/
   ```

1. Download configuration templates:

   ```shell
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O $MONITOR_WORKSPACE/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O $MONITOR_WORKSPACE/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O $MONITOR_WORKSPACE/blackbox_exporter/blackbox.yml
   ```

1. Edit `$MONITOR_WORKSPACE/prometheus/prometheus.yml`:

   ```yaml
   alerting:
     alertmanagers:
       - static_configs:
           - targets:
               - alertmanager:9093
   ```

   ```yaml
   scrape_configs:
     - job_name: "prometheus"
       static_configs:
         - targets: ["localhost:9090"]

     - job_name: "pushgateway"
       static_configs:
         - targets: ["pushgateway:9091"]

     - job_name: "alertmanager"
       static_configs:
         - targets: ["alertmanager:9093"]

     - job_name: "blackbox_exporter"
       static_configs:
         - targets: ["blackbox_exporter:9115"]

     - job_name: "grafana"
       static_configs:
         - targets: ["grafana:3000"]

     - job_name: "karma"
       static_configs:
         - targets: ["karma:8080"]
   ```

1. (Optional) Load rules in the [rules](https://github.com/rea1shane/monitor/tree/main/rules) folder:

   1. Edit `docker-compose.yaml`:

      ```yaml
      services:
        prometheus:
          volumes:
            - $MONITOR_WORKSPACE/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
            - ./rules:/etc/prometheus/rules
      ```

   1. Edit `$MONITOR_WORKSPACE/prometheus/prometheus.yml`, make Prometheus load rules:

      ```yaml
      rule_files:
        - /etc/prometheus/rules/*.yml
        - /etc/prometheus/rules/*.yaml
      ```

1. (Optional) Custom configuration files. Edit following files if you want:

   - `$MONITOR_WORKSPACE/prometheus/prometheus.yml`
   - `$MONITOR_WORKSPACE/alertmanager/alertmanager.yml`
   - `$MONITOR_WORKSPACE/blackbox_exporter/blackbox.yml`

### Run

Just run:

```shell
docker-compose up
```
