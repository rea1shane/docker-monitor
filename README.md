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

1. Download template of configuration files:

   ```shell
   # Make workspace
   mkdir -p ~/tmp/monitor/prometheus/alertmanager/
   mkdir -p ~/tmp/monitor/prometheus/blackbox_exporter/

   # Download template configuration files
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O ~/tmp/monitor/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O ~/tmp/monitor/prometheus/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O ~/tmp/monitor/prometheus/blackbox_exporter/blackbox.yml
   ```

2. Edit `~/tmp/monitor/prometheus/prometheus.yml`:

   ```diff
     ...

     # Alertmanager configuration
     alerting:
       alertmanagers:
         - static_configs:
             - targets:
   -           # - alertmanager:9093
   +           - alertmanager:9093

     ...

     # A scrape configuration containing exactly one endpoint to scrape:
     # Here it's Prometheus itself.
     scrape_configs:
       # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
       - job_name: "prometheus"

         # metrics_path defaults to '/metrics'
         # scheme defaults to 'http'.

         static_configs:
           - targets: ["localhost:9090"]
   +
   +   - job_name: "pushgateway"
   +     static_configs:
   +       - targets: ["pushgateway:9091"]
   +
   +   - job_name: "blackbox_exporter"
   +     static_configs:
   +       - targets: ["blackbox_exporter:9115"]
   ```

3. (Optional) Custom configuration files. Edit following files if you want:

   - `~/tmp/monitor/prometheus/prometheus.yml`
   - `~/tmp/monitor/prometheus/alertmanager/alertmanager.yml`
   - `~/tmp/monitor/prometheus/blackbox_exporter/blackbox.yml`

4. (Optional) Load Prometheus alerts. For example, using the alert files in the [alerts](https://github.com/rea1shane/monitor/tree/main/alerts) folder:

   1. Edit `docker-compose.yaml`:

      ```diff
        ...

        services:
          prometheus:

            ...

            volumes:
              - ~/tmp/monitor/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      +       - ./alerts:/etc/prometheus/alerts

        ...
      ```

   2. Edit `~/tmp/monitor/prometheus/prometheus.yml`, make Prometheus load alert rules:

      ```diff
        ...

        # Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
        rule_files:
      -   # - "first_rules.yml"
      -   # - "second_rules.yml"
      +   - /etc/prometheus/alerts/*.yml
      +   - /etc/prometheus/alerts/*.yaml

        ...
      ```

### Run

Just run:

```shell
docker-compose up
```
