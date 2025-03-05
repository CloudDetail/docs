import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

**APO is divided into two parts:**

- **APO-server:** The server-side component of APO.
- **APO-one-agent:** The client-side component of APO, used to collect information about monitored services, clusters, and hosts in Kubernetes clusters or virtual machine environments.

<Tabs>
  <TabItem value="kubernetes" label="Kubernetes" default>

This document contains instructions for installing and running APO-one-agent on Kubernetes using Helm Chart. Please ensure that you have [installed APO-Server](/docs/Installation/APO%20Server.md) before installing APO-one-agent.

> **Note:** It is not recommended to install APO-server and APO-one-agent in the same Kubernetes cluster using the method described in this document, as it may lead to unnecessary resource waste. If you wish to deploy APO-server and APO-one-agent in the same Kubernetes cluster, please refer to the [Quick Start](/docs/Quick%20Start.md) documentation.

APO-one-agent comes with default probe versions for various programming languages:
1. Java agent: [opentelemetry-java-instrumentation v2.8.0](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/tag/v2.8.0)
2. Python agent: [opentelemetry-python 1.27.0/0.48b0](https://github.com/open-telemetry/opentelemetry-python/releases/tag/v1.27.0)
3. NodeJs agent: [opentelemetry-js v1.24.1/0.51.1](https://github.com/open-telemetry/opentelemetry-js)
4. Go agent: [Grafana Beyla v1.8.4](https://github.com/grafana/beyla/releases/tag/v1.8.4)

> Note: For Java applications, APO defaults to using Opentelemetry v2.8.0 to collect trace data. If you wish to leverage OneAgent's auto-injection capability to automatically inject custom Trace probes (e.g., Skywalking or other versions of Opentelemetry) into your services, please refer to [Advanced: Replace OneAgent Otel Probe with Custom Tracing Probe](/docs/Installation/Advanced/Replace%20OneAgent%20Otel%20Probe%20with%20Custom%20Tracing%20Probe.md) to build a custom probe image and configure it.

## Prerequisites
To install APO using Helm, ensure that the following steps have been completed:

- Install a Kubernetes server on your computer. For information on installing Kubernetes, refer to [Installing Kubernetes](https://kubernetes.io/docs/setup/).
- Install the latest stable version of Helm. For information on installing Helm, refer to [Installing Helm](https://helm.sh/docs/intro/install/).
- Network Policy: Ensure that the cluster/machine where APO-one-agent is located can access ports 30044, 31363, 30317, and 30319 of the APO-server cluster.

## Setting Up the APO Helm Repository
To set up the APO Helm repository so that you can download the correct APO Helm charts on your computer, follow these steps:
**Use the following command to add the** `apo` **Helm repository:**
```bash
helm repo add apo https://apo-charts.oss-cn-hangzhou.aliyuncs.com
helm repo update apo
```

## Configuring the APO-one-agent Component
Create a file named `apo-one-agent-values.yaml`, define it according to your requirements, and mount the configuration during the deployment phase.
```bash
# apoServerIP is the ClusterIP where APO-server is located
global:
  apoServerIP: x.x.x.x # FIXME

# APO-one-agent enabled flag, default is false
odigos:
  instrumentor:
    # targetNamespace 
    # name: target namespace
    # value:
    #   enabled: inject all existing services, but do not inject subsequent new applications
    #   enabledFuture: inject all current and future services
    #   disabled: do not inject services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
    targetNamespace:
    - name: default
      value: enabledFuture
    - name: default2
      value: enabled
    - name: default3
      value: disabled
    # instrument-all-namespace whether to inject all namespaces
    # equivalent to setting enabledFuture for all namespaces
    # but if ns or workload is already set to disabled, it will not be injected
    instrumentAllNamespace: false
    # force-instrument-all-namespace whether to forcibly inject all namespaces
    # similar to instrument-all-namespace, all ns are set to enabledFuture
    # and all disabled settings are ignored
    forceInstrumentAllNamespace: false

# Grafana-Beyla startup flag, default is false
# Beyla runs as a Go language Trace probe in OneAgent, default is off, requires system kernel 5.8 or higher
# For running conditions, refer to the official Grafana-Beyla documentation: https://grafana.com/docs/beyla/latest/
# grafanaBeyla:
#   enabled: true
```

Here is the basic configuration provided for APO deployment. If you need to make more configuration changes, please download the `values.yaml` file from the APO Helm Charts repository:
```bash
helm show values apo/apo-one-agent > values.yaml
```
## Deploy APO-one-agent
Run the following command to deploy APO in the specified namespace.
```bash
helm install apo-one-agent apo/apo-one-agent -n apo --create-namespace \
-f apo-one-agent-values.yaml
```

:::warning

After successful installation, the target monitored service must be restarted!!!

**If you encounter issues**:

- If `apo-one-agent` is not ready and is in the `6/7` or `6/8` state, you can further troubleshoot by referring to the documents [《Resolving ebpf-agent Not Ready Issue》](/docs/Troubleshooting/eBPF-Agent%20Unready%20Issue.md) and [《Resolving grafana-beyla Not Ready Issue》](/docs/Troubleshooting/Grafana-Beyla%20Unready%20Issue.md).

:::

## Uninstall APO-one-agent
```bash
helm uninstall apo-one-agent -n apo
kubectl delete ns apo
```

## Deploy APO-one-agent Community Edition
By default, APO installs the Enterprise Edition, which provides more data analysis capabilities compared to the Community Edition. For differences, please refer to [Version Differences](/docs/About%20APO/What%20is%20APO.md).

:::danger[Announcement]

The Enterprise Edition features of APO are currently in free public beta🔥!!!

:::

If needed, you can also use the Community Edition by adding the following parameter to the installation command:
```
--set global.edition=ce
```

  </TabItem>
  <TabItem value="traditional" label="Traditional">

This document contains instructions for installing and running APO-one-agent on traditional servers (non-Kubernetes). Please ensure that you have [installed APO-Server](/docs/Installation/APO%20Server.md) before installing APO-one-agent.

APO-one-agent comes with default probe versions for various programming languages:
1. Java agent: [opentelemetry-java-instrumentation v2.8.0](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/tag/v2.8.0)
2. Python agent: [opentelemetry-python 1.27.0/0.48b0](https://github.com/open-telemetry/opentelemetry-python/releases/tag/v1.27.0)
3. NodeJs agent: [opentelemetry-js v1.24.1/0.51.1](https://github.com/open-telemetry/opentelemetry-js)
4. Go agent: [Grafana Beyla v1.8.4](https://github.com/grafana/beyla/releases/tag/v1.8.4)

> Note: For Java applications, APO defaults to using Opentelemetry v2.8.0 to collect trace data. If you wish to leverage OneAgent's auto-injection capability to automatically inject custom Trace probes (e.g., Skywalking or other versions of Opentelemetry), please refer to [Advanced: Replace OneAgent Otel Probe with Custom Tracing Probe](/docs/Installation/Advanced/Replace%20OneAgent%20Otel%20Probe%20with%20Custom%20Tracing%20Probe.md) to build a custom probe image and configure it.

## Download the Installation Package
By default, the latest version is downloaded here. This document will use the latest version as an example.
```bash
export APO_VERSION=latest
# export APO_VERSION=v0.0.000 # You can specify your version

wget https://apo-ce.oss-cn-hangzhou.aliyuncs.com/apo-one-agent-compose-${APO_VERSION}.tgz
```
## Deploy APO-one-agent
Run the following command to deploy the traditional server version of APO-one-agent:
```bash
export APO_VERSION=latest
export APO_PROXY_IP=<YOUR APO-server NodeIP> # FIXME
```

```bash
tar -zxvf apo-one-agent-compose-$APO_VERSION.tgz
cd apo-one-agent-compose/
bash deploy.sh init $APO_SERVER_IP
```
## Verification
If you see an output similar to the following, it indicates a successful installation:
```bash
==================================================
      ___           ___           ___
     /\  \         /\  \         /\  \
    /::\  \       /::\  \       /::\  \
   /:/\:\  \     /:/\:\  \     /:/\:\  \
  /::\~\:\  \   /::\~\:\  \   /:/  \:\  \
 /:/\:\ \:\__\ /:/\:\ \:\__\ /:/__/ \:\__\
 \/__\:\/:/  / \/__\:\/:/  / \:\  \ /:/  /
      \::/  /       \::/  /   \:\  /:/  /
      /:/  /         \/__/     \:\/:/  /
     /:/  /                     \::/  /
     \/__/                       \/__/
Official Website:   https://apo.kindlingx.com/
DEPLOY_VERSION: <version>
APO_ONE_AGENT_VERSION:
==================================================
INFO: APO-server-ip = <YOUR APO-server NodeIP>
 Container apo-otel-collector-agent  Started
 Container apo-grafana-alloy  Started
 Container apo-node-agent  Started
 Container apo-preload-agent  Started
 Container apo-go-sdk-auto  Started
 Container apo-ilogtail  Started
 Container apo-ebpf-agent  Started
```
Run the following command to check if the containers have started successfully:
```bash
bash deploy.sh list

# You should see an output similar to the following
NAME                 IMAGE                                                                 COMMAND                   SERVICE              CREATED          STATUS          PORTS
apo-ebpf-agent       registry.cn-hangzhou.aliyuncs.com/originx/ebpf-agent:v1.5.0           "sh start.sh"             apo-ebpf-agent       5 minutes ago   Up 5 minutes
apo-go-sdk-auto      registry.cn-hangzhou.aliyuncs.com/originx/sdk-auto:v1.0.0             "/app/originx-sdk-au…"   apo-go-sdk-auto      5 minutes ago   Up 5 minutes
apo-grafana-alloy    registry.cn-hangzhou.aliyuncs.com/originx/grafana-alloy:v1.2.1        "/bin/alloy run --st…"   apo-grafana-alloy    5 minutes ago   Up 5 minutes
apo-ilogtail         registry.cn-hangzhou.aliyuncs.com/originx/ilogtail:v1.5.0             "/usr/local/ilogtail…"   apo-ilogtail         5 minutes ago   Up 5 minutes
apo-node-agent       registry.cn-hangzhou.aliyuncs.com/originx/node-agent:v1.5.0           "/app/pinger"             apo-node-agent       5 minutes ago   Up 5 minutes
apo-otel-collector   registry.cn-hangzhou.aliyuncs.com/originx/apo-otel-collector:v0.1.0   "./otelcol --config …"   apo-otel-collector-agent   5 minutes ago   Up 5 minutes
```

:::warning

After successful installation, you need to restart the target monitored service!!!

**If you encounter issues**:

- If the status of apo-ebpf-agent and apo-grafana-beyla is abnormal, you can refer to the documents [《Resolving ebpf-agent Not Ready Issue》](/docs/Troubleshooting/eBPF-Agent%20Unready%20Issue.md) and [《Resolving grafana-beyla Not Ready Issue》](/docs/Troubleshooting/Grafana-Beyla%20Unready%20Issue.md) for further troubleshooting.

:::

## Restart the Monitored Application
### Non-container Applications
Simply restart the application to automatically monitor it.
### Docker Container Applications
After confirming that APO-one-agent has started, you need to configure and restart the Docker application according to [《Using one-agent in Docker Containers》](https://github.com/CloudDetail/preload?tab=readme-ov-file#%E5%9C%A8docker%E5%AE%B9%E5%99%A8%E5%86%85%E4%BD%BF%E7%94%A8).

## Uninstall APO-one-agent
```bash
bash deploy.sh stop
```

  </TabItem>
</Tabs>