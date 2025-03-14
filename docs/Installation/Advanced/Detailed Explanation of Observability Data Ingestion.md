The APO guided observability platform provides users with a one-stop solution for observability data visualization and analysis. APO not only displays data collected through APO collectors but **also integrates with existing monitoring data, enabling the use of a modern observability platform without requiring extensive modifications to the current monitoring system**.

The types of monitoring data that APO supports can be categorized as follows:

+ Tracing
+ Metrics
+ Logs
+ Alert Events

## Tracing
APO offers OneAgent to automatically inject tracing probes into applications, eliminating the need for manual configuration. For Java applications, if you already have tracing probes in place, APO supports integrating existing tracing data through a connection approach, while also enabling alert analysis capabilities.

### Integration Option 1: Using Existing Probes, APO Connects to Tracing Query APIs

#### Supported Tracing Systems
+ Opentelemetry Java Instrument 1.11.x and above
+ SkyWalking 8.x and above
+ PinPoint 1.8.0 and above
+ Elastic APM 1.x

#### Integration Method
+ APO requires an Agent to be installed on each host to gather process information on the host.
+ The APO server needs to connect to the query API of the Tracing system to retrieve individual trace data.
+ Advantages: No need to modify the existing Tracing system.
+ Disadvantages: Only supports Java; lacks metrics for external request calls from the application.

#### Detailed Steps
Install APO OneAgent in `trace-sidecar` mode and provide the Tracing query API address when installing the APO Server.

### Integration Option 2: Using Existing Probes, Sending Tracing Data to APO

#### Supported Tracing Probes
+ Opentelemetry Java Instrument 1.11.x and above
+ SkyWalking 8.x and above
+ Other probes that support OTLP format for exporting traces.

#### Integration Method
+ APO requires an Agent to be installed on each host to gather process information on the host.
+ Modify the data sending address of the probe to the local APO OneAgent address.
+ Advantages: Supports various programming languages like Java, Go, Python; enables full functionality of APO.
+ Disadvantages: Requires adjusting the data sending address of the Tracing probe.

#### Detailed Steps
Install APO OneAgent in `trace-collector` mode, then modify the data sending address of the existing probe to the APO collector address, specifically:
+ For traces supporting OTLP format:
  + If using gRPC protocol, use `http://<local IP>:4317`
  + If using HTTP protocol, use `http://<local IP>:4318`
+ For SkyWalking:
  + If using gRPC protocol, use `http://<local IP>:11800`
  + If using HTTP protocol, use `http://<local IP>:12800`

## Metrics
APO automatically collects metric data from the environment through OneAgent and stores it in VictoriaMetrics. By default, the following metrics are collected:

+ node-exporter: Host-level metrics
+ cadvisor: Container-level metrics
+ kubelet: Kubernetes kubelet metrics

Given the low resource consumption for collecting and storing such metrics, it is recommended to directly use APO OneAgent for data collection. If you already collect these metrics and store them in a Prometheus-like database and wish to avoid re-collecting data with APO OneAgent, APO also supports integration with existing Prometheus-like databases.

### Integration Option 1: APO Platform Connects to Existing Metric Storage Systems

#### Supported Storage Systems
+ Prometheus
+ VictoriaMetrics
+ Thanos

#### Integration Methods
+ When installing APO, modify the VictoriaMetrics address to the Prometheus-like database address (supports Prometheus and VictoriaMetrics), refer to the [Integration Document](/docs/Installation/Advanced/Integrate%20with%20Existing%20VictoriaMetrics.md)
+ If you do not need to repeatedly collect the above metrics, please disable the `metrics` module of APO OneAgent during installation

### Integration Solution Two: Use Existing Metric Collectors to Send Data to APO
#### Supported Data Formats
+ OTLP format (HTTP/gRPC)
+ Prometheus standard format

#### Integration Methods
+ If your metric collector supports OTLP format output
    - If the output protocol is HTTP, modify the data sending address to `http://<APO-IP>:30317`
    - If the output protocol is gRPC, modify the data sending address to `http://<APO-IP>:30318`
+ If your metric collector uses Prometheus format output (exporter type uses HTTP endpoint /metrics to expose metrics)
    - Open the configuration file `apo-grafana-alloy-config` in APO, and refer to the [Configuration Document](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.scrape/) to add the address of the metric collector to the configuration file

## Full Logs
APO can automatically collect logs from applications in the environment through OneAgent and store them in ClickHouse. APO currently has two solutions for integrating full logs: receiving data from existing log collectors or directly querying logs in the database.

### Integration Solution One: Use Existing Log Collectors to Send Data to APO
#### Supported Log Collectors
+ ilogtail
+ logstash
+ fluent
+ vector

#### Integration Methods
+ Modify the data sending address of the existing log collector
+ Rename the fields of the collected logs in the APO vector

### Integration Solution Two: APO Platform Integrates with Existing Log Databases
This solution currently only supports integration with logs stored in ClickHouse.

#### Integration Methods
+ When installing APO, modify the ClickHouse address to the address where the logs are stored. Refer to the [Document](/docs/Installation/Advanced/Integrate%20with%20Existing%20Clickhouse.md)

## Alert Events
If you are already using an alert platform to generate alerts but wish to use APO's alert analysis function to analyze all alerts, you can integrate external alert events into the APO platform.

Currently, the APO platform only supports integrating alerts generated by AlertManager. Other alert platforms are being adapted.

### Integration Steps
1. Open the configuration file of AlertManager
2. Add a new `receiver` in the `receivers` section, named `apo-alert-collector`, and configure this receiver in the `route` section as follows:

```yaml
global:
  resolve_timeout: 5m
receivers:
- name: apo-alert-collector
  webhook_configs:
  - url: http://apo-backend-svc:8080/api/alerts/inputs/alertmanager
route:
  group_by:
  - alertname
  group_interval: 1m
  group_wait: 30s
  receiver: alert-collector
  repeat_interval: 30m

```

3. Call the `GET /-/reload` interface of AlertManager to reload the configuration file or restart AlertManager to make the configuration effective.
