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
// Collect Redis Metrics
prometheus.exporter.redis "example" {
  redis_addr = "<redis-url>:6379"
  redis_password = "password"
}

prometheus.scrape "redis" {
  targets    = prometheus.exporter.redis.example.targets
  forward_to = [otelcol.receiver.prometheus.default.receiver]
}
```
Modify the following configurations as needed:
- redis_addr: Redis address
- redis_password: Redis password

### 3. Check Metric Data
After modifying the configuration file, restart the probe. To verify if the metric data is successfully collected, open the APO page, click on the "Middleware Dashboard" tab, open the Elasticsearch monitoring dashboard, and check the metrics.

## More
For more configurable options, please refer to the [documentation](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.exporter.redis/).
