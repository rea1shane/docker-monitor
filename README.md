# Docker monitor

项目包含以下组件：

- [Prometheus](https://github.com/prometheus/prometheus)
- [Blackbox exporter](https://github.com/prometheus/blackbox_exporter)
- [Grafana](https://github.com/grafana/grafana)
- [Alertmanager](https://github.com/prometheus/alertmanager)
- [karma](https://github.com/prymitive/karma)

## 准备工作

### 必要部分

为了能让项目正确的运行，以下内容必须要完成。

1. 下载模板配置文件：

   ```shell
   # 创建工作区
   mkdir -p ~/tmp/monitor/prometheus/alertmanager/
   mkdir -p ~/tmp/monitor/prometheus/blackbox_exporter/

   # 下载模板配置文件
   wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O ~/tmp/monitor/prometheus/prometheus.yml
   wget https://raw.githubusercontent.com/prometheus/alertmanager/main/examples/ha/alertmanager.yml -O ~/tmp/monitor/prometheus/alertmanager/alertmanager.yml
   wget https://raw.githubusercontent.com/prometheus/blackbox_exporter/master/blackbox.yml -O ~/tmp/monitor/prometheus/blackbox_exporter/blackbox.yml
   ```

2. 修改 `~/tmp/monitor/prometheus/prometheus.yml`：

   ```yaml
   alerting:
     alertmanagers:
       - static_configs:
           - targets:
               # 指定 Alertmanager
               - alertmanager:9093

   scrape_configs:
     # 添加 pushgateway 到监控列表
     - job_name: "pushgateway"
       static_configs:
         - targets: ["pushgateway:9091"]

     # 添加 blackbox_exporter 到监控列表
     - job_name: "blackbox_exporter"
       static_configs:
         - targets: ["blackbox_exporter:9115"]
   ```

### 可选部分

以下的内容均为非必须项，根据自己需要选择性操作。

#### 添加 Prometheus 告警规则

1. 拉取包含告警规则的仓库，这里使用 [monitor](https://github.com/rea1shane/monitor) 仓库中的告警规则：

   ```shell
   git clone https://github.com/rea1shane/monitor.git ~/tmp/monitor/resources
   ```

2. 修改 `~/tmp/monitor/prometheus/prometheus.yml`，让 Prometheus 加载告警规则：

   ```yaml
   rule_files:
     - /etc/prometheus/rules/*.yml
     - /etc/prometheus/rules/*.yaml
   ```
