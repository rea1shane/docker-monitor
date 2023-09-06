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

1. Designate a workspace:

   ```shell
   # e.g.
   export MONITOR_WORKSPACE=~/tmp/monitor
   ```

1. Download template of configuration files:

   ```shell
   # Set up workspace
   mkdir -p $MONITOR_WORKSPACE/prometheus/alertmanager/
   mkdir -p $MONITOR_WORKSPACE/prometheus/blackbox_exporter/

   # Download template configuration files
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O $MONITOR_WORKSPACE/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O $MONITOR_WORKSPACE/prometheus/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O $MONITOR_WORKSPACE/prometheus/blackbox_exporter/blackbox.yml
   ```

1. Edit `$MONITOR_WORKSPACE/prometheus/prometheus.yml`:

   ```yaml
   alerting:
     alertmanagers:
       - static_configs:
           - targets:
               - alertmanager:9093

   scrape_configs:
     - job_name: "pushgateway"
       static_configs:
         - targets: ["pushgateway:9091"]

     - job_name: "blackbox_exporter"
       static_configs:
         - targets: ["blackbox_exporter:9115"]
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
   - `$MONITOR_WORKSPACE/prometheus/alertmanager/alertmanager.yml`
   - `$MONITOR_WORKSPACE/prometheus/blackbox_exporter/blackbox.yml`

### Run

Just run:

```shell
docker-compose up
```
