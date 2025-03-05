RabbitMQ currently offers two methods for integrating monitoring data.

### Method 1: Using the Built-in Prometheus Plugin (Recommended)
**Version Requirement:** RabbitMQ (version >= 3.8.0)

**Steps:**

1. In the running environment of the RabbitMQ service, enable the Prometheus plugin using the following command:
```
rabbitmq-plugins enable rabbitmq_prometheus
```

2. Add the following configuration to `configmap/apo-grafana-alloy-config`, replacing the `YOUR_RABBITMQ_SERVER_IP` variable:

```plain
prometheus.scrape "rabbitmq" {
  targets = [
    {
      __address__ = "YOUR_RABBITMQ_SERVER_IP:15692",
      job = "integrations/rabbitmq",
    },
  ]
  scheme     = "http"
  scrape_interval = "60s"
  forward_to = [prometheus.relabel.example.receiver]
}
```
Note: When replacing `YOUR_RABBITMQ_SERVER_IP`, do not include the http or https prefix, e.g., 192.168.1.1:15692.

3. Use the `RabbitMQ Official Metrics` dashboard included in APO Grafana to view monitoring data.

### Method 2: Using the Community Open-Source Exporter (Integrated into Alloy)
**Version Requirement:** RabbitMQ (version >= 3.6.8), earlier versions may also be supported. Refer to the [Configuration Modification Instructions](https://github.com/kbudde/rabbitmq_exporter?tab=readme-ov-file#common-errors--faq).

**Steps:**

1. Add the following configuration to `configmap/apo-grafana-alloy-config`. Below is the most basic configuration, replace the `YOUR_RABBITMQ_URL` variable:

```plain
prometheus.exporter.rabbitmq "rabbitmq_demo" {
    rabbit_url = YOUR_RABBITMQ_URL
}

prometheus.scrape "rabbitmq_demo" {
  targets    = prometheus.exporter.rabbitmq.rabbitmq_demo.targets
  forward_to = [prometheus.relabel.example.receiver]
}
```

2. Use the `RabbitMQ Metrics (kbudde/rabbitmq_exporter)` dashboard included in APO Grafana to view monitoring data.

**Configuration Notes:**

The `prometheus.exporter.rabbitmq` block supports the following optional configurations, all of which are sourced from [`kbudde/rabbitmq_exporter`](https://github.com/kbudde/rabbitmq_exporter?tab=readme-ov-file#configuration).

| Environment variable | default | description |
| --- | --- | --- |
| rabbit_url | [http://127.0.0.1:15672](http://127.0.0.1:15672) | URL to RabbitMQ management plugin (must start with http(s)://) |
| rabbit_user | guest | Username for RabbitMQ management plugin. User needs monitoring tag! |
| rabbit_password | guest | Password for RabbitMQ management plugin |
| rabbit_connection | direct | Direct or loadbalancer, strips the self label when loadbalancer |
| rabbit_user_file |  | Location of file with username (useful for Docker secrets) |
| rabbit_password_file |  | Location of file with password (useful for Docker secrets) |
| publish_port | 9419 | Listening port for the exporter |
| publish_addr | "" | Listening host/IP for the exporter |
| output_format | TTY | Log output format. TTY and JSON are supported |
| log_level | info | Log level. Possible values: "debug", "info", "warning", "error", "fatal", or "panic" |
| cafile | ca.pem | Path to root certificate for access management plugin. Only needed if a self-signed certificate is used. Will be ignored if the file does not exist |
| certfile | client-cert.pem | Path to client certificate used to verify the exporter's authenticity. Will be ignored if the file does not exist |
| keyfile | client-key.pem | Path to private key used with certificate to verify the exporter's authenticity. Will be ignored if the file does not exist |
| skipverify | false | true/0 will ignore certificate errors of the management plugin |
| skip_vhost | ^$ | Regex, matching vhost names are not exported. First performs INCLUDE_VHOST, then SKIP_VHOST. Applies to queues and exchanges |
| include_vhost | .* | Regex vhost filter. Only matching vhosts are exported. Applies to queues and exchanges |
| include_queues | .* | Regex queue filter. Only matching names are exported |
| skip_queues | ^$ | Regex, matching queue names are not exported (useful for short-lived RPC queues). First performed INCLUDE, after SKIP |
| include_exchanges | .* | Regex exchange filter. (Only exchanges in matching vhosts are exported) |
| skip_exchanges | ^$ | Regex, matching exchange names are not exported. First performed INCLUDE, after SKIP |
| rabbit_capabilities | bert,no_sort | Comma-separated list of extended scraping capabilities supported by the target RabbitMQ server |
| rabbit_exporters | exchange,node,queue | List of enabled modules. Possible modules: connections, shovel, federation, exchange, node, queue, memory |
| rabbit_timeout | 30 | Timeout in seconds for retrieving data from management plugin. |
| max_queues | 0 | Max number of queues before we drop metrics (disabled if set to 0) |
| exclude_metrics |  | Metric names to exclude from export. Comma-separated. e.g. "recv_oct, recv_cnt". See exporter_*.go for names |

Certainly! Since the original text provided is empty, there's nothing to translate. If you have any specific content you'd like translated into English, please provide it, and I'll be happy to assist!
