---
slug: /
---

## What is AutoPilot Observability (APO)?
- **Out-of-the-box Observability Platform**: APO is dedicated to providing a one-click installation, out-of-the-box observability platform. APO's OneAgent supports one-click, configuration-free installation of Tracing probes, enabling the collection of application fault logs, infrastructure metrics, network metrics of applications and downstream dependencies, and Kubernetes events. It also supports the collection of data such as [Polaris Causal Metrics](https://one.kindlingx.com) based on eBPF. It allows querying Tracing data using Jaeger UI and querying Metrics data from VictoriaMetrics using PromQL, which can then be displayed on Grafana.
- **OpenTelemetry Distribution Integrated with eBPF Technology**: APO integrates OpenTelemetry probes, otel-collector, Jaeger, ClickHouse, and VictoriaMetrics. With OneAgent technology, it supports **automatic installation** of multi-language OpenTelemetry probes in both traditional server and container environments, reducing user configuration efforts. APO innovatively combines eBPF technology with the OpenTelemetry ecosystem, significantly reducing data storage requirements based on a retrospective sampling algorithm.
- **eBPF-based Guided Troubleshooting Product**: AutoPilot aims to assist users in isolating and identifying the root cause of faults. APO provides a guided interface to help users isolate and identify fault causes. By installing OpenTelemetry probes using APO's OneAgent, OneAgent can analyze environmental faults, retain fault scene data using eBPF technology, and display it on the guided troubleshooting interface. eBPF technology enables the guided troubleshooting interface to assist users in isolating and identifying fault causes on a single page.

## Features
### Automatic Installation of OpenTelemetry Probes, No Manual Configuration Required
With OneAgent technology, APO supports automatic installation of multi-language OpenTelemetry probes in both traditional server and container environments, reducing user configuration efforts.
### One-stop Observability Hub, Fault Localization on a Single Page
APO integrates data such as traces, metrics, logs, and events, providing a one-stop solution for observability and fault localization needs.

### Alert Analysis Functionality, Automatically Correlating Data for Quick Fault Diagnosis
The alert analysis functionality automatically categorizes alerts and correlates data, precisely linking alerts to applications and business entry points. By deeply analyzing alerts at business entry points, users can examine the relationship between alert events and services. "Alert Playback" uses topology to logically replay alert events, helping users locate fault nodes. Finally, by examining the latency and error reports of fault nodes, users can understand the current issues and their causes. These features provide users with comprehensive alert and fault analysis capabilities, reducing the time and cost of fault diagnosis.

### Business Interface-level Topology
APO distinguishes between different interface calls of the same application, clearly showing the call relationships when the application executes certain business logic. The same application node may appear multiple times in the call sequence. Since the complete topology can be too complex and lacks the "map navigation" function to guide users to suspected fault nodes, APO uses latency curve similarity to filter out nodes with low similarity, displaying more nodes in tabular form to avoid overly complex topologies. When users need to view downstream dependency nodes, they can click on the node name to quickly switch to the detail page of different nodes.

### Efficient Identification of Cascading Fault Nodes Based on Similarity Algorithm Sorting
When request latency faults occur, many nodes are affected in a cascading manner. Traditional alerts show that many nodes have alerts. In APO, each node matches the latency of its downstream dependencies with similarity curves to find the node with the most similar latency. The node with the highest similarity is more likely to be the root cause. The downstream dependencies include both direct downstream and dependencies of downstream dependencies.

### Polaris Causal Metrics Root Cause Determination Algorithm
Simply analyzing trace data leaves many blind spots, making it difficult to quickly determine whether latency increases are caused by the application itself or its dependencies. The [Polaris Causal Metrics](https://one.kindlingx.com) root cause algorithm directly identifies the cause of latency fluctuations, providing a direction for fault diagnosis. For example, the figure below shows that the main cause is the change in external network call latency leading to application latency changes. By combining network latency metrics, it can be determined whether the cause is network latency changes or downstream node latency changes.

The Polaris Causal Metrics collected using eBPF technology directly provide fault localization directions, broadly categorized as follows:

- Too many nested loops in the code
- Code locks (garbage collection, contention for data connection pool locks)
- Storage failures (disk failures)
- Downstream dependencies (middleware failures, service failures); need to exclude network quality issues based on network metrics
- Insufficient CPU resources (other programs hogging the CPU or unreasonable container limit configurations)
- Insufficient memory

### Quickly Locate Fault Chains and Logs
By analyzing latency, error rates, and the number of log errors, you can quickly pinpoint the time when a fault might have occurred, allowing you to review logs or chain data around that time.

## Features
### Retaining Hot Data from Fault Scenes
Hot data from fault scenes vs. traditional observability data

- For abnormal (slow or erroneous) chain data, retrospective sampling is performed while retaining logs from the same period (referred to as **fault scene logs**). These fault scene data are known as hot data in APO. The retrospective sampling algorithm can be referenced in the [algorithm paper](https://www.usenix.org/conference/nsdi23/presentation/zhang-lei).
- The traditional observability retention method is referred to as cold data retention.

The association based on hot data from fault scenes provides an extremely smooth experience for jumping between different data. Additionally, the hot data from fault scenes offers more possibilities for future storage optimization and cost savings in observability.

### Fearless Data Expansion, Efficiently Associating and Jumping Between Data
APO uses eBPF technology to generate and save "index" data for raw data. The size of the "index" data is significantly smaller than the raw data. Even if the raw data expands, APO can still quickly provide observability data results by querying the index data. Meanwhile, eBPF technology is used to add metadata tags to different types of data in the kernel, enhancing the correlation between data and allowing users to seamlessly jump between different data.

Compared to other observability solutions, APO occupies less storage cost while providing data results faster for the same time period.

### Event Indicator Linkage and More Intuitive Fault Alerts

- **Log Error Count Indicator**: Traditional log alerting is based on log content such as "Exception" or "error". However, logs often output Exceptions even when the business is running normally, leading to false alerts if based solely on log content. To avoid this, APO only matches "Exception" or "error" logs and turns these counts into indicators. If this indicator spikes in a short time, it can serve as a log alert, indicating issues in the program execution.
- **Network Quality Eventization**: APO quickly assesses network quality based on ping packet latency. If the ping packet delay exceeds a predefined threshold, the network quality status on the APO page will provide a prompt.
- **Polaris Causal Indicator**: The [Polaris Causal Indicator](https://one.kindlingx.com) is an indicator of the latency distribution of various resource types during the execution of business processes. Based on the latency distribution, algorithms can determine whether a latency spike is due to the application itself or its dependencies. Issues within the application can also indicate the direction of the fault cause, such as code loops, locks, or insufficient resources.
- **Alert Information Association**: Traditional alert information has little linkage with observability data. In APO, infrastructure status, network quality status, and K8s event status are converted into alert information, helping users to quickly determine the cause of faults within the same interface.

## Version Differences

<table style={{ width: "100%", textAlign: "center" }}>
  <thead>
    <tr>
      <th></th>
      <th>Specific Features</th>
      <th>Community Edition</th>
      <th>Enterprise Edition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" >Observability Features</td>
      <td>Logs and Related Features</td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
    </tr>
    <tr>
      <td>Metrics and Related Features</td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
    </tr>
    <tr>
      <td>Traces and Related Features</td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
    </tr>
    <tr>
      <td rowspan="2" >Analytics Based on Observability Data</td>
      <td>Alert Analysis</td>
      <td><img src="/img/uncheck-img.png" alt="uncheck" width="25"/></td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
    </tr>
    <tr>
      <td>More Analytics Features in the Future</td>
      <td><img src="/img/uncheck-img.png" alt="uncheck" width="25"/></td>
      <td><img src="/img/check-img.png" alt="check" width="25"/></td>
    </tr>
  </tbody>
</table>
