The APO Guided Observability Platform provides users with a one-stop solution for displaying and analyzing observability data. APO is not only capable of showcasing data collected through the APO Collector, but **it can also integrate existing monitoring data, allowing the use of a modern observability platform without the need for extensive modifications to the current monitoring system**.

:::info
To integrate with an existing monitoring system, certain integration requirements must be met, and some APO functionalities may be unavailable. For specific requirements and limitations, please refer to the document [Detailed Explanation of Observability Data Ingestion](/docs/Installation/Advanced/Detailed%20Explanation%20of%20Observability%20Data%20Ingestion.md).
:::

The types of monitoring data that APO supports can be categorized as follows:

+ **Tracing**: If you are already using a tracing probe, there is no need to reinstall APO's Tracing Injection Tool.
+ **Metrics**: If you are already using a Prometheus-like system to collect and store metrics, there is no need to reinstall the Metrics Collection Tool, and you can use the existing Prometheus-like system as APO's metrics storage.
+ **Logs**: If you are already collecting logs and storing them in ClickHouse, you can use this ClickHouse as APO's storage and view the complete logs within APO.

This document provides installation solutions for **common scenarios** to integrate existing monitoring data into the APO platform. To integrate with an existing monitoring system, **you need to adjust the installation configuration file during the APO installation process** to adapt to the corresponding environment. **The overall installation process is the same as installing the full version of APO. Please configure the APO components according to the instructions provided below during the "Configure APO Components" phase.**

## APO Server Installation and Configuration Guide
When installing the APO Server using Helm, a configuration file is required for the installation. Adjusting the configuration items in the file enables **integration with existing metric storage systems** and **existing logs**.
:::info
The following section only provides the parts of the configuration file that need to be adjusted. For the complete configuration items, please refer to the [Complete Configuration Document](/docs/Installation/APO%20Server.md).
:::
### Integrating Existing Trace Data
If you wish to integrate existing trace data on the APO platform, please configure the `apoApmAdapter` section when installing the APO Server. Adjust the `apo-values.yaml` configuration file as shown below:
```yaml
apo-collector:
  apoApmAdapter:
    config:
      adapter:
        # Expose HTTP port number.
        http_port: 8079
        # Set HTTP request timeout, return directly if timeout.
        timeout: 10
        # APM system integration.
        trace_api:
          # Enable APM integration list
          apm_list: [skywalking, jaeger, elastic]
          # Skywalking configuration
          skywalking:
            # Use port 12800, no http or https prefix needed
            address: ""
            user: ""
            password: ""
          # Jaeger configuration
          # No http or https prefix needed
          jaeger:
            address: apo-jaeger-collector-svc:16686/jaeger
          # Tingyun3 related configuration
          nbs3:
            # Use http port, no http or https prefix needed
            address: ""
            user: ""
            password: ""
          # Arms related configuration
          arms:
            # Use http port, no http or https prefix needed
            address: "arms.cn-hangzhou.aliyuncs.com"
            access_key: ""
            access_secret: ""
          elastic:
            # Protocol prefix 'https://' or 'http://' can be added, default is http
            address: ""
            user: ""
            password: ""
          # Pinpoint configuration
          pinpoint:
            # Use http port, no http or https prefix needed
            address: ""
```

Please add the **trace system type** you are using to the `apm_list` according to your actual situation, and configure the address, username, password, and other parameters for the corresponding trace system. Then, use this configuration file to install the APO Server to integrate with the existing trace data.

Note that after installing the APO Server in this mode, you need to use the `trace-sidecar` mode when installing the APO OneAgent. For details, see the [APO OneAgent Installation and Configuration Guide](#apo-oneagent-installation-and-configuration-guide).

:::info
For the limitations and effects of this installation solution, please refer to the document [Trace Integration Solution One](/docs/APO%20向导式可观测性中心/企业版相关/数据接入能力详细说明#接入方案一使用现有探针apo-对接链路追踪查询-api)
:::

### Integrating Existing Metric Data
If you wish to view existing metrics on the APO platform, please use an existing Prometheus-like system as the metric storage system when installing the APO Server. Adjust the configuration file as shown below:

```yaml
# victoriaMetricsUrl: Hosted VictoriaMetrics access address
# If your VictoriaMetrics has basic authentication, include the username and password in the URL
# If your password contains special characters, convert them to URL-encoded characters
# For example, with username apo and password Apo@123456
# global:
#   victoriaMetricsUrl: "http://apo:Apo%40123456@apo-victoria-metrics-single-server-svc:8428"
global:
  victoriaMetricsUrl: "http://apo-victoria-metrics-single-server-svc:8428" # FIXME

# Configure APO-server not to create a VictoriaMetrics instance
victoria-metrics-single:
  enabled: false
```

### Integrating Log Data Stored in ClickHouse
If you wish to view existing logs on the APO platform, please use the ClickHouse that stores the logs when installing the APO Server. Adjust the configuration file as shown below:

```yaml
# clickhouseUrl: Hosted Clickhouse access address
# clickhouseHttpUrl: Hosted Clickhouse HTTP access address
# clickhouseRootUsername: Hosted Clickhouse access username, requires double quotes
# clickhouseRootPassword: Hosted Clickhouse access password, requires double quotes
# clickhosueDatabase: Hosted Clickhouse access database name, no need to modify unless necessary, requires double quotes
global:
  clickhouseUrl: "apo-clickhouse-svc:9000" # FIXME
  clickhouseHttpUrl: "apo-clickhouse-svc:8123" # FIXME
  clickhouseRootUsername: "apo-user" # FIXME
  clickhouseRootPassword: "Apo@123456" # FIXME
  clickhosueDatabase: "apo"

## Configure APO components
# APO-server configuration
# Configure APO-server not to create a Clickhouse instance
altinity-clickhouse-operator:
  enabled: false
```

## APO OneAgent Installation Configuration Instructions
When installing APO OneAgent using Helm, use the following configuration file for installation:

```yaml
global:
  agentCollectorMode:
    # Trace collection mode
    # - trace Full collection, automatically injects Trace collection probes
    # - trace-collcetor Uses collector to accept external Trace data from probes
    # - trace-sidecar Does not accept external Trace data, directly reads data from external APIs. This mode requires configuring external API data sources
    - trace
    - metrics
    - log
```

By combining and adjusting the options in `agentCollectorMode`, various usage scenarios for accessing data can be satisfied.
### Trace-related
Please fill in
+ Include `trace`: Install the Tracing probe auto-injection tool, suitable for scenarios **without prior use** of Tracing probes.
+ Include `trace-sidecar`: Suitable for scenarios where Tracing probes are already in use and **do not wish to modify** the Traces data sending address. The Traces data address needs to be configured when installing the APO Server.
+ Include `trace-collector`: Suitable for scenarios where Tracing probes are already in use and **can modify** the Traces data sending address. The data sending address of the existing probe needs to be modified. For details, see [Trace Integration Plan Two](/docs/APO%20Wizard-Observability%20Center/Enterprise%20Edition%20Related/Detailed%20Data%20Access%20Capabilities#Access%20Plan%20Two%20Using%20Existing%20Probes%20to%20Send%20Trace%20Data%20to%20APO).
+ Exclude `trace`, `trace-sidecar`, and `trace-collector`: Suitable for scenarios where Traces and application request metrics are not required.

### Metrics-related
+ Include `metrics`: Install the Metrics collection tool, suitable for scenarios requiring the collection of basic environmental monitoring metrics.
    - If this option is **not included**, metrics will not be collected. If you wish to view existing metrics on the APO platform, please use an existing Prometheus-like system as the metrics storage system when installing the APO Server.

### Log-related
+ Include `log`: Install the log collection tool, suitable for scenarios requiring the collection of fault scene logs or full logs.
    - It is recommended to always collect fault scene logs, as the data volume is small and resource consumption is low.
    - If this option is **not included**, log data will not be collected. If you wish to view existing logs on the APO platform, please use ClickHouse, which stores the logs, when installing the APO Server.
