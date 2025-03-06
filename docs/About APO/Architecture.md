---
sidebar_position: 0
---

## Architecture Diagram



The overall architecture of APO can be divided into the following layers:
- **Data Collection Layer**: Includes data probes and data collection agents, responsible for collecting data from various monitoring systems and log systems. It also supports the use of existing data collectors.
- **Data Processing Layer**: Cleans, filters, and aggregates the collected data to ensure accuracy and consistency. Utilizes machine learning and big data analysis techniques to deeply analyze the data, identifying anomalies and root causes of faults.
- **Storage Layer**: Employs distributed databases and time-series databases to ensure efficient and scalable data storage.
- **Application Layer**: Provides a visual user interface, including service overview, link tracing, log retrieval, metric panels, anomaly detection, alert analysis, and fault troubleshooting wizards.

## Data Flow Diagram

APO mainly consists of three components: APO OneAgent, APO Proxy, and APO Server:
- **APO OneAgent**: The data collection layer, deployed on each monitored host, used to collect information about monitored services and clusters in Kubernetes or virtual machine environments.
- **APO Proxy**: The data processing layer, deployed within the monitored cluster, used to receive and process data at the monitored edge and uniformly forward it to the server.
- **APO Server**: The data processing and storage layer, used to receive, process, analyze, and display data.

The following diagram illustrates the core data flow of the guided observability product's data collection, processing, and storage layers:



Below is an introduction to the functions of the components in the data flow diagram.

### APO OneAgent
- **preload/odigos**: Used to install Tracing probes for user applications.
- **ebpf-agent**: Used to obtain polaris metrics.
- **ilogtail**: Used to collect application log data.
- **node-agent**: Used to collect network metrics.
- **grafana-alloy**: Used to collect node metric data.
- **otel-collector-agent**: Used to collect Traces data, calculate RED metrics for services, and database call metrics.

### APO Proxy
- **nginx-proxy**: Used to receive Traces and Profilings from monitoring nodes and uniformly forward them to the server.
- **otel-collector**: Used to collect Kubernetes events; receive data from monitoring nodes and forward it to the server; supplement Kubernetes metadata in metrics.

### APO Server
- **apo-collector**: Used to collect Traces and Profilings, analyze data to identify link faults, and store data in ClickHouse.
- **otel-collector-gateway**: Used to uniformly receive Traces, Metrics, and Logs data on the server.
- **jaeger-collector**: Used to receive Traces data and convert it into Jaeger's data format.
- **remote-storage**: Used to store Traces in Jaeger format in ClickHouse. Jaeger 1.58 does not support directly writing link data to ClickHouse and querying it. APO modified Jaeger RemoteStorage to write to ClickHouse and query links according to the Jaeger Clickhouse project format. The integrated Jaeger version is currently 1.58.
- **jaeger-query**: Used to query Traces data.
- **ClickHouse**: An open-source columnar database management system (DBMS) for online analytical processing (OLAP). It is designed for high-speed queries and data analysis, capable of running on a single server or cluster, and supports real-time data stream processing. ClickHouse can handle large amounts of structured data and can query billions of rows of data in seconds.
- **VictoriaMetrics**: A time-series database for monitoring and time-series data processing, particularly suitable for large-scale monitoring scenarios. Its main goal is to provide high-performance time-series storage and retrieval capabilities while maintaining low resource consumption.

For readability, some components used by the APO Server are not displayed in the data flow diagram. Here is a description of the other components:
- **apo-backend** serves as the API interface for the application layer.
- **apo-frontend/apo-grafana** is used for visualizing data and providing a user interface for the application layer.
- **vmalert** is used to query metrics in VictoriaMetrics and generate alerts.
- **alertmanager** is responsible for receiving alert information and managing alerts, such as alert notifications and alert suppression.
