This document is used to troubleshoot the issue of no data displayed on the APO platform's frontend.

Please complete the first section [Common Basic Troubleshooting](#common-basic-troubleshooting) first, and then proceed to select different sections based on your needs for further troubleshooting.

## Common Basic Troubleshooting
![Board](/img/Service%20Overview%20No%20Data%20img-0.jpg)

1. After deploying APO-server and APO-one-agent, ensure all components are in the `Running` state.
   - If `apo-one-agent` is not ready and is in the `6/7` or `6/8` state, you can refer to the documents [eBPF-Agent Unready Issue](/docs/Troubleshooting/eBPF-Agent%20Unready%20Issue.md) and [Grafana-Beyla Unready Issue](/docs/Troubleshooting/Grafana-Beyla%20Unready%20Issue.md) for further troubleshooting.
2. Confirm that the monitored service is within the target namespace of APO-one-agent. Verify if the service is correctly identified. APO-one-agent will automatically recognize applications within the target namespace. If successful, it will label the `deployment` resource of the service `pod` with `odigos-instrumentation=enabled`.
    1. If the `Deployment` resource of the service you expect to monitor does not have the `odigos-instrumentation=enabled` label, please refer to the section [How to Modify the Automatic Monitoring Scope of APO-one-agent in Kubernetes Version](#how-to-modify-the-automatic-monitoring-scope-of-apo-one-agent-in-kubernetes-version) for modifications.
3. If the service was deployed after APO-one-agent, **you need to wait for the service to complete its first startup, then restart the application** to inject the monitoring configuration.
4. If the service was already running in the environment before APO-one-agent was deployed, manually restart the application after APO-one-agent is successfully deployed to complete the monitoring configuration injection.
5. Ensure that the service with the injected monitoring configuration has requests to generate Trace data.
6. Confirm that the time selector in the top right corner of the APO frontend is set to the latest time. After the first successful deployment of APO-server, the APO component pods are in the `running` state, but internal initialization may still be ongoing. Please wait for about 3 minutes, then select the time selector in the top right corner of the frontend and choose the last 5 minutes. Additionally, there is a delay between the generation of monitoring data and its display on the frontend, expected to be within 1 minute.

## Is the Monitoring Configuration Successfully Injected for Java Applications?
This section is based on the premise that after completing the first section [Common Basic Troubleshooting](#common-basic-troubleshooting), the frontend still does not display monitoring data for JAVA applications.

APO-one-agent includes the default JAVA collection probe version: [opentelemetry-java-instrumentation v2.8.0](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/tag/v2.8.0)

**Troubleshooting Process:**

1. If the JAVA application itself is already configured with Trace probes such as SkyWalking or OTel, conflicts may arise. It is recommended to remove the original Trace probe configuration.
2. In a K8s environment, after restarting the JAVA application, enter the JAVA service pod and execute the commands `env | grep JAVA_TOOL_OPTIONS` and `env | grep OTEL_EXPORTER_OTLP_ENDPOINT`. The expected output should be `JAVA_TOOL_OPTIONS=-javaagent:/var/odigos/java/javaagent.jar` and `OTEL_EXPORTER_OTLP_ENDPOINT=http://<nodeIP>:4317`, where `nodeIP` is the IP of the node where the pod is located.
    1. If the expected environment variables are not output here, you may have encountered an unexpected issue. [Contact Us](#contact-us) for assistance in troubleshooting.
3. In a virtual machine environment, execute `java --version`. The expected output should be something like `Picked up JAVA_TOOL_OPTIONS: -javaagent:/etc/apo/instrument/agent/java/opentelemetry/v2.5.0/opentelemetry-javaagent.jar`.
    1. If the expected output is not seen here, you may have encountered an unexpected issue. Please [Contact Us](#contact-us) for assistance in troubleshooting.

## Is the Go Application Successfully Injected with Monitoring Configuration?
This section is based on the premise that after completing the first chapter [Common Basic Issues Troubleshooting](#common-basic-troubleshooting), the frontend still does not display Go application monitoring data.

APO-one-agent includes the default Go collection probe version: [opentelemetry-go-instrumentation v0.13.0-alpha](https://github.com/CloudDetail/opentelemetry-go-instrumentation/commits/apo/)

**Please ensure your Go version is within the compatibility range of the default version.**

+ Linux with kernel version 4.19 or higher
+ Go 1.18 or higher

**Troubleshooting Process:**

1. In a K8s environment, check the logs of the go-sdk-auto container in the APO-one-agent component on the node where your Go service to be monitored is located. Use the command `kubectl logs apo-one-agent-xxxxx -c go-sdk-auto -n apo` to query `Error` level logs for the cause of the failure.
    1. If there is no expected error output here, you may have encountered an unexpected issue. Please [Contact Us](#contact-us) for assistance in troubleshooting.

## Is the NodeJS Application Successfully Injected with Monitoring Configuration?
This section is based on the premise that after completing the first chapter [Common Basic Issues Troubleshooting](#common-basic-troubleshooting), the frontend still does not display Go application monitoring data.

APO-one-agent includes the default NodeJS collection probe version: [opentelemetry-js v1.24.1/0.51.1](https://github.com/open-telemetry/opentelemetry-js)

**Please ensure your NodeJS version is within the compatibility range of the default version.**

## Is the Python Application Successfully Injected with Monitoring Configuration?
This section is based on the premise that after completing the first chapter [Common Basic Issues Troubleshooting](#common-basic-troubleshooting), the frontend still does not display Go application monitoring data.

APO-one-agent includes the default Python collection probe version: [opentelemetry-python 1.23.0/0.44b0](https://github.com/open-telemetry/opentelemetry-python/releases/tag/v1.23.0)

**Please ensure your Python version is within the compatibility range of the default version.**

## How to Modify the Automatic Injection Monitoring Scope of APO-one-agent in Kubernetes Version
There are two methods to modify the automatic injection monitoring scope of APO-one-agent:

#### Method 1:
If you have already deployed APO-one-agent, you can use the `helm upgrade` command to update your deployment configuration.

**Note:** The `helm upgrade` command will initialize all configurations that were modified outside of Helm.

+ If you deployed APO-one-agent using the *Quick Start* guide, modify the code in lines 16~36 of your `apo-values.yaml` to suit your environment:

```bash
# APO-server 
# Persistent storage configuration, recommended to enable, default is false
# If persistent storage is enabled, you need to create PVs for ClickHouse and VictoriaMetrics based on PVCs
altinity-clickhouse-operator:
  clickhouse:
    persistence:
      enabled: true
victoria-metrics-single:
  server:
    persistentVolume:
      enabled: true

# APO-one-agent 
apo-one-agent:
  enabled: true
  odigos:
    instrumentor:
      # targetNamespace 
      # name: Target namespace
      # value:
      #   enabled: Inject all existing services, but do not inject newly added applications
      #   enabledFuture: Inject all current and future services
      #   disabled: Do not inject services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
      targetNamespace:
      - name: default
        value: enabledFuture
      - name: default2
        value: enabled
      # instrument-all-namespace Whether to inject all namespaces
      # Equivalent to setting enabledFuture for all namespaces
      # However, if ns or workload is already set to disabled, it will not be injected
      instrumentAllNamespace: false
      # force-instrument-all-namespace Whether to forcibly inject all namespaces
      # Similar to instrument-all-namespace, all namespaces are set to enabledFuture
      # And all disabled settings are ignored
      forceInstrumentAllNamespace: false
```

After updating and saving your configuration file, use the following command to update the probe:

```bash
helm upgrade apo apo/apo -n apo \
-f apo-values.yaml
```

+ If you deployed APO-one-agent using the *Monitoring Kubernetes Cluster Servers and Applications with OneAgent Default OTEL Probe Version* or *Monitoring Kubernetes Cluster Servers and Applications with OneAgent Custom Probe Version* guides, modify the code in lines 6~28 of your `apo-one-agent-values.yaml` to suit your environment:

```bash
# apoServerIP is the ClusterIP where APO-server is located
global:
  apoServerIP: x.x.x.x # FIXME
```

# APO-one-agent Enabled Flag, Default is false
odigos:
  instrumentor:
    # targetNamespace 
    # name: Target Namespace
    # value:
    #   enabled: Inject all existing services, but do not inject newly added applications
    #   enabledFuture: Inject all current and future services
    #   disabled: Do not inject services under the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
    targetNamespace:
    - name: default
      value: enabledFuture
    - name: default2
      value: enabled
    - name: default3
      value: disabled
    # instrument-all-namespace Whether to instrument all namespaces
    # Equivalent to setting enabledFuture for all namespaces
    # However, if ns or workload is already set to disabled, it will not be instrumented
    instrumentAllNamespace: false
    # force-instrument-all-namespace Whether to forcibly instrument all namespaces
    # Similar to instrument-all-namespace, sets enabledFuture for all namespaces
    # And ignores all disabled settings
    forceInstrumentAllNamespace: false
```

After updating and saving your configuration file, use the following command to update the agent:

```bash
helm upgrade apo-one-agent apo/apo-one-agent -n apo \
-f apo-one-agent-values.yaml
```

#### Method 2:
If you have already deployed APO-one-agent and do not wish to use `helm upgrade` to update the configuration.

You will need to manually modify the APO-one-agent component configuration. Execute the following command in the cluster where APO-one-agent is installed:

```bash
kubectl edit cm apo-instrumentor-config -n apo
```

Expected output is shown below. Modify the code between lines 35~65 to adapt to your environment:

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: apo-instrumentor-config
  namespace: apo
  uid: xxxxxxxxxxxxxxxxxx
  resourceVersion: 'xxxxxx'
  creationTimestamp: 'xxxxxxxxxxxxxxxxxxx'
  labels:
    app.kubernetes.io/managed-by: Helm
  annotations:
    meta.helm.sh/release-name: apo
    meta.helm.sh/release-namespace: apo
  managedFields:
    - manager: helm
      operation: Update
      apiVersion: v1
      time: 'xxxxxxxxxxxxxxxxxx'
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          .: {}
          f:instrument-conf.yaml: {}
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/managed-by: {}
  selfLink: /api/v1/namespaces/apo/configmaps/apo-instrumentor-config
data:
  instrument-conf.yaml: |
    # Currently, none of the settings below will ever operate on the kube-system namespace.

    # Configure the namespaces to be instrumented
    # enabled: Instrument all existing services, but do not instrument newly added applications
    # enabledFuture: Instrument both existing and future services
    # disabled: Do not instrument services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
    # e.g 
    # namespace:
    #   train-ticket: enabledFuture
    #   originx: enabled
    namespace:
      default: enabledFuture
      default2: enabled

    # Configure the workloads to be instrumented within the namespace
    # This setting takes precedence over the namespace settings and is typically used to enable instrumentation for specific workloads or to ignore specific workloads when instrumenting a namespace
    # e.g
    workload:
      apo:
        deployment/apo-java-demo: enabled
      # default:
      #   deploymen/demo: disabled
```

```markdown
# instrument-all-namespace Whether to inject all namespaces
# Equivalent to setting enabledFuture for all namespaces
# However, if a namespace or workload has already been set to disabled, it will not be injected
instrument-all-namespace: false
# force-instrument-all-namespace Whether to forcibly inject all namespaces
# Similar to instrument-all-namespace, sets enabledFuture for all namespaces
# and ignores all disabled settings
force-instrument-all-namespace: false
```

After saving your changes, use the following command to restart the component for the changes to take effect:

```bash
kubectl rollout restart deployment apo-odigos-instrumentor -n apo
```

## Contact Us
If you encounter any unexpected issues during troubleshooting, you can reach out to us through official channels, and we will provide assistance as soon as possible.
- [GitHub Issues](https://github.com/CloudDetail/apo/issues)
