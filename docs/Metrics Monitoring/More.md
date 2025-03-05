APO utilizes [Grafana Alloy](https://grafana.com/docs/alloy/latest/) to collect middleware metrics. You can refer to the [Alloy Configuration Documentation](https://grafana.com/docs/alloy/latest/) to gather various middleware metrics.

## Standard Configuration Steps
### 1. Open the Configuration File
#### Kubernetes Environment
```
kubectl edit cm apo-grafana-alloy-config -n apo
```
#### Traditional Server Environment
The default path for the configuration file is `config/grafana-alloy/config.alloy` under the probe installation directory.

### 2. Configure Monitoring Items
Refer to the Alloy Configuration Documentation to add the corresponding middleware monitoring items in the configuration file.
Note that in addition to the middleware configuration, you also need to add the `prometheus.scrape` configuration section. For example, if the configuration name is `Demo`, add the following configuration:
```js
prometheus.scrape "Demo" {
  targets    = prometheus.exporter.elasticsearch.example.targets
  forward_to = [otelcol.receiver.prometheus.default.receiver]
}
```

### 3. Check Metric Data
After modifying the configuration file, you need to restart the probe. To verify whether the metric data is successfully collected, open the APO page, click on the "Middleware Dashboard" tab, and open the monitoring dashboard to view the metrics.
