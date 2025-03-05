## Steps
### 1. Open the Configuration File
#### Kubernetes Environment
```
kubectl edit cm apo-grafana-alloy-config -n apo
```
#### Traditional Server Environment
The default path for the configuration file is `config/grafana-alloy/config.alloy` under the probe installation directory.

### 2. Configure Monitoring Items
Add the following content to the configuration file:

```js
// Collect MySQL Metrics
prometheus.exporter.mysql "example" {
  data_source_name  = "username:password@(<mysql-url>:3306)/"
  enable_collectors = ["heartbeat", "mysql.user"]
}

prometheus.scrape "mysql" {
  targets    = prometheus.exporter.mysql.example.targets
  forward_to = [otelcol.receiver.prometheus.default.receiver]
}
```
Modify the following configurations as needed:
- data_source_name: MySQL address

### 3. Check Metric Data
After modifying the configuration file, restart the probe. To verify if the metric data is successfully collected, open the APO page, click on the "Middleware Dashboard" tab, open the Elasticsearch monitoring dashboard, and check the metrics.

## More
For more configurable options, please refer to the [documentation](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.exporter.mysql/).
