**APO is divided into two parts:**

- **APO-server:** The server-side component of APO.
- **APO-one-agent:** The client-side component of APO, used to collect information about monitored services, clusters, and hosts in Kubernetes clusters or virtual machine environments.

This document contains instructions for installing and running APO-server on a Kubernetes cluster using Helm Chart.

> **Note:** It is not recommended to install APO-server and APO-one-agent in the same Kubernetes cluster using the method described in this document, as it may lead to unnecessary resource waste. If you wish to deploy APO-server and APO-one-agent in the same Kubernetes cluster, please refer to the [Quick Start](/docs/Quick%20Start.md) document.

## Prerequisites

1. A minimum hardware resource of 4 cores and 8GB of memory is required. Disk resources depend on your monitoring scale, with a minimum of 50GB.
2. To install APO using Helm, ensure the following steps are completed:
   - Install a Kubernetes server on your machine. For information on installing Kubernetes, refer to the official documentation [Installing Kubernetes](https://kubernetes.io/docs/setup/).
   - Install the latest stable version of Helm. For information on installing Helm, refer to [Installing Helm](https://helm.sh/docs/intro/install/).
3. **Network Firewall Configuration:** Open the necessary ports as described in [Appendix 1: Network Port List](#appendix-1-network-port-list).
4. If you are using a K8s cluster provided by a cloud service provider: It is recommended to follow the [Production Environment Deployment Recommendations](/docs/Installation/Advanced/) to **manage APO's ClickHouse and VictoriaMetrics** or **use non-persistent configurations**. For details, see: [Cloud Provider Cluster Database Creation Failure](/docs/Troubleshooting/Cloud%20provider%20cluster%20failed%20to%20create%20the%20database.md).

## Setting Up the APO Helm Repository

To set up the APO Helm repository to download the correct APO Helm charts on your machine, complete the following steps:

**Use the following commands to add the `apo` Helm repository:**
```bash
helm repo add apo https://apo-charts.oss-cn-hangzhou.aliyuncs.com
helm repo update apo
```

## Configuring APO Components

Create a file named `apo-values.yaml` and define it according to your requirements, then mount the configuration during the deployment phase.
```yaml
# APO-server configuration
# Persistent configuration, recommended to enable, default is false
# If persistent configuration is enabled, create PV for the component based on PVC
altinity-clickhouse-operator:
  clickhouse:
    persistence:
      enabled: true
victoria-metrics-single:
  server:
    persistentVolume:
      enabled: true
apo-backend:
  persistence: 
    enabled: true
```

## Deploying APO Helm Charts

Run the following command to deploy APO in the specified namespace.
```bash
helm install apo apo/apo -n apo --create-namespace \
-f apo-values.yaml
```

## Verification

Enter the following command to check if the pods have started successfully:
```bash
kubectl get po -n apo
```

:::info

**Encountering Issues**: You can refer to [《Troubleshooting》](/docs/Troubleshooting/) for further troubleshooting, or contact us.

:::

## Accessing APO
You can access APO through the following methods, where `NodeIP` is the IP address of any node in the cluster.
- APO Guided Observability Platform Address: `http://<NodeIP>:31364`; Default Username/Password: admin/APO2024@admin

![img-1](/img/APO%20Server%20img-1.png)

- Grafana Address: `http://<NodeIP>:31364/#/system-dashboard`

![img-2](/img/APO%20Server%20img-2.png)

## Next Steps: Monitoring Servers and Applications
Please refer to the documentation to install `APO-one-agent` for monitoring servers and applications:
- [Kubernetes Installation of OneAgent](/docs/Installation/APO%20OneAgent.md): Use Helm Chart to install and run `APO-one-agent` on Kubernetes, monitoring services within the Kubernetes cluster.
- [Traditional Server Installation of OneAgent](/docs/Installation/APO%20OneAgent.md): Supports monitoring traditional services and services started with Docker.
- [Production Environment Deployment Recommendations](/docs/Installation/Advanced/): Some recommended deployment configurations for deploying APO in a production environment.

## Updates and Uninstallation
### Updating APO Helm Charts Configuration
If your APO configuration has been updated, use the following command to update after modifying the `apo-values.yaml` file:
```bash
helm upgrade apo apo/apo -n apo \
-f apo-values.yaml
```

### Uninstalling APO Helm Charts
```bash
kubectl delete clickhouseinstallation apo -napo
helm uninstall apo -n apo
kubectl delete ns apo
```

### Additional Configurations
This document provides the basic configuration for APO deployment. If you need to make more configuration changes, download the `values.yaml` file from the APO Helm Charts repository:
```bash
helm show values apo/apo > values.yaml
```

## Appendix
### Appendix 1 Network Port List

- APO-server needs to expose the following ports to desktop browsers for access, all of which can be changed through deployment configurations:

| Component Name | Open Port | Description |
| --- | --- | --- |
| apo-front | 31364 | APO Guided Observability Platform Frontend |

- APO-server needs to expose the following ports to APO-one-agent nodes for data collection, all of which can be changed through deployment configurations:

| Component Name | Open Port | Description |
| --- | --- | --- |
| apo-collector | 30044 | For receiving trace sampling data | 
| apo-vector | 30310 | For receiving and processing log data |
| apo-otel-collector-gateway | 30317 | For receiving observability data, OTLP gRPC protocol | 
| apo-otel-collector-gateway | 30319 | For receiving K8s cluster data, OTLP gRPC protocol | 
| apo-backend | 31363 | For providing query API services |

### Appendix 2: Deploying APO Community Edition

By default, APO installs the Enterprise Edition, which offers enhanced data analysis capabilities compared to the Community Edition. For details on the differences, please refer to the [Version Comparison](/docs/About%20APO/What%20is%20APO.md).

:::danger[Notice]

The Enterprise Edition features of APO are currently in free public beta testing! 🔥

:::

If you prefer to use the Community Edition, please add the following parameter to the installation command:
```
--set global.edition=ce
```
