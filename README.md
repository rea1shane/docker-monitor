# Monitor

Blueprint for Prometheus-based monitoring system. The following components are included:

- [Prometheus - The Prometheus monitoring system and time series database.](https://github.com/prometheus/prometheus)
- [Pushgateway - Push acceptor for ephemeral and batch jobs.](https://github.com/prometheus/pushgateway)
- [Blackbox exporter - Blackbox prober exporter.](https://github.com/prometheus/blackbox_exporter)
- [Grafana - The open and composable observability and data visualization platform.](https://github.com/grafana/grafana)
- [Alertmanager - Prometheus Alertmanager.](https://github.com/prometheus/alertmanager)
- [Node exporter - Exporter for machine metrics.](https://github.com/prometheus/node_exporter)

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
   mkdir -p $MONITOR_WORKSPACE/grafana/
   ```

1. Download configuration templates:

   ```shell
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O $MONITOR_WORKSPACE/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O $MONITOR_WORKSPACE/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O $MONITOR_WORKSPACE/blackbox_exporter/blackbox.yml
   wget https://raw.githubusercontent.com/grafana/grafana/main/conf/sample.ini -O $MONITOR_WORKSPACE/grafana/grafana.ini
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
     - job_name: "file_ds"
       file_sd_configs:
         - refresh_interval: 5m
           files:
             - /etc/prometheus/targets.json
   ```

1. (Optional) Load rules in the [rules](https://github.com/rea1shane/monitor/tree/main/rules) folder:

   1. Edit `docker-compose.yaml`:

      ```yaml
      services:
        prometheus:
          volumes:
            - ./rules:/etc/prometheus/rules # Add this line
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
   - `$MONITOR_WORKSPACE/grafana/grafana.ini`

### Run

Just run:

```shell
docker-compose up
```

> [!IMPORTANT]
>
> If you are in a shell environment without the `MONITOR_WORKSPACE` variable, you need to re-export it first.
>
> ```shell
> # Set the same path as in the preparation session.
> export MONITOR_WORKSPACE=~/tmp/monitor
> ```

### Dashboards

Here are some shared dashboards:

- [Prometheus 2.0 Overview | Grafana Labs](https://grafana.com/grafana/dashboards/3662-prometheus-2-0-overview/)
- [Blackbox Exporter (HTTP prober) | Grafana Labs](https://grafana.com/grafana/dashboards/13659-blackbox-exporter-http-prober/)
- [Grafana Internals | Grafana Labs](https://grafana.com/grafana/dashboards/3590-grafana-internals/)
- [Alertmanager | Grafana Labs](https://grafana.com/grafana/dashboards/9578-alertmanager/)
- [Node Exporter Full | Grafana Labs](https://grafana.com/grafana/dashboards/1860-node-exporter-full/)

More shared dashboards: [Dashboards | Grafana Labs](https://grafana.com/grafana/dashboards/)
