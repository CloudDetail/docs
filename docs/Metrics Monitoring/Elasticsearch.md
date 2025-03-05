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
// Collect Elasticsearch Metrics
prometheus.exporter.elasticsearch "example" {
  address = "http://<elasticsearch-url>:9200"
  basic_auth {
    username = USERNAME
    password = PASSWORD
  }
}

prometheus.scrape "elasticsearch" {
  targets    = prometheus.exporter.elasticsearch.example.targets
  forward_to = [otelcol.receiver.prometheus.default.receiver]
}
```
Modify the following configurations as needed:
- address: Elasticsearch address, e.g., `http://192.168.0.1:9200`
- username: Elasticsearch username
- password: Elasticsearch password

### 3. Check Metric Data
After modifying the configuration file, restart the probe. To verify if the metric data is successfully collected, open the APO page, click on the "Middleware Dashboard" tab, open the Elasticsearch monitoring dashboard, and check the metrics.

## More
For more configurable items, please refer to the [documentation](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.exporter.elasticsearch/).
