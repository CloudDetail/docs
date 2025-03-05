---
sidebar_position: 0
---

## Display Rules for Services and Request Endpoints
The homepage displays service names and request endpoints, sorted according to the following rules:

- Request endpoints with latency and error rates exceeding thresholds on a daily or weekly basis will be ranked higher. Within the same service, request endpoints that exceed thresholds will be ranked higher. If no thresholds are exceeded, endpoints are sorted alphabetically and may not appear on the homepage, requiring a click on "More" to view them.
- If different service nodes have request endpoints with latency or error rates exceeding thresholds, they are sorted based on subsequent service-level alerts: the service with the most red alerts will be ranked higher. If the number of alerts is the same, services are sorted alphabetically. Log alerts and infrastructure alerts carry the same weight.

## Quickly Identify Suspected Faulty Services
According to the display rules for services and request endpoints, the top-ranked service request endpoint is the one users need to inspect.
### Quickly Determine if Business is Normal
Based on the latency and error rate of the service request endpoint, if there are no significant changes compared to the previous period, the business represented by the service request endpoint can be considered normal, even if there are some alerts for this service node, such as infrastructure alerts or log alerts. Whether to immediately click on the request endpoint details depends on the company's stability strategy. If you want to prevent issues early, you can click on the details and follow the subsequent sections to troubleshoot.

Conversely, if the latency and error rate of the request endpoint exceed the threshold compared to the previous period, the business is considered abnormal, even if there are no alerts for this service. In this case, you should immediately click on the request endpoint details to quickly diagnose and locate the fault.

### If Multiple Services Have Simultaneous Latency and Error Rate Spikes, How to Quickly Identify the Most Suspicious Service Node?
Each service's request endpoint has a latency source: there are two options, either itself or a dependency.

If a service's request endpoint latency source is itself, the service is highly suspicious. Otherwise, the latency source is mainly downstream dependencies, which could be middleware or dependent microservice nodes.

If all service request endpoint latency sources are dependencies rather than themselves, but latency and error rates are still spiking, the fault likely originates from middleware or databases that all services depend on. In this case, simply click on the top-ranked service request endpoint details, and within the fault time range, find any trace to see which middleware was accessed, allowing you to quickly identify problematic middleware.

## Data Correspondence for Alerts Across Different Service Dimensions
### Log Alert Surge
Indicates a surge in exceptions and errors reflected in the logs. Click on the service request endpoint details, find the error instances tab.

Expand this tab, then select the time period corresponding to the surge to view the logs and analyze what happened.

### Infrastructure Alert Turns Red
Indicates that the infrastructure (CPU, memory, disk usage, network bandwidth usage) where the service instance resides has exceeded thresholds. Click on the service request endpoint details, find the user-defined dashboard.

Expand this tab, focus on analyzing these metrics to see which instances' machine resources have exceeded thresholds, then log in to the machine to investigate the specific root cause.

### Network Alert Turns Red
Indicates that the network quality between the service node and downstream dependency nodes has been consistently poor for over 1 minute with more than 10ms latency. Since network quality issues are mostly unrelated to request endpoints and affect a wide area rather than just two points, network alerts are placed at the service level rather than the request endpoint level. Click on the service request endpoint details, find the user-defined dashboard.

Expand this tab, focus on analyzing the network latency curve to identify which two IPs have high latency. If on a public cloud, submit a ticket; if in a self-built data center, hand it over to the network maintenance team for further investigation.

### K8S Event Turns Red
Indicates that the pod where the service instance resides has experienced critical K8S events. Click on the service request endpoint details, find the K8S events tab.

Expand this tab, focus on K8S-related events, such as pod restarts, exits, OOM, etc.

### Deployment or Restart Time
If the latency and error rate of the service request endpoint exceed thresholds compared to the previous period, and the deployment time is close to the fault occurrence time, the fault is likely caused by deployment changes. In this case, you can perform a rollback. You can also click on the service request endpoint details to see what changes were made. Focus on the logs and Polaris metrics in the error instances tab to understand the cause of the latency.

## Service Request Endpoint Details Usage Instructions
Identify more suspicious faulty services based on topology and latency curves.

### Upstream and Downstream Topology
Since topology structures become very difficult to use when there are numerous microservice nodes, APO currently only displays the upstream and downstream services in the details to confirm if there have been any changes in these services.

### Dependency Node Latency Curve Comparison Chart
Viewing many services in the topology graph is challenging, but displaying latency curves on a single chart makes it much easier. Simply check if the dependency latency curves are similar to the service latency curve being viewed. The more similar the dependency service curves are, the more suspicious the dependency service is, and it should be prioritized for investigation.

If none of the dependency service latency curves are similar, there is no need to investigate downstream service dependencies.

### Usage of Polaris Causal Metrics for Root Cause
If an increase in error rate is confirmed on the homepage, the error instances tab should be checked, focusing on finding the cause of the fault from the logs. If there is an increase in latency, Polaris causal metrics should be focused on. Polaris causal metrics help determine the source of the latency. Since eBPF-collected kernel data might be difficult to understand, here are the corresponding code directions:
 - High CPU time: Too many nested loops in the code
 - High GC and lock: Code locks (including GC, data connection pool contention locks are here)
 - High file: Storage failure (disk failure) or reading/writing very large files
 - High net and epoll: Downstream dependencies (middleware failure, service failure) exclude network quality faults based on ping packet data
 - RunQ: Insufficient CPU resources (other programs preempting CPU or unreasonable container limit configurations)
 - Other: Insufficient physical memory

### Log and Trace Viewing
APO should have already helped users define and locate faults before these two tabs, but in some scenarios, users may still want to view logs and traces. Fault scene traces and logs related to this service can be checked in these two tabs.

The approach to viewing logs and traces is the same:

Users will be presented with log alert curves, latency curves, and error rate change curves over time. Based on these curve changes, users can find key time points to view the fault scene traces or logs at those times.

What are key time points?
Generally, key time points can be selected as follows:

- The highest point of the curve
- The time point with the greatest change in curve slope

Users can also choose different time points to view fault scene logs and traces based on their understanding.
