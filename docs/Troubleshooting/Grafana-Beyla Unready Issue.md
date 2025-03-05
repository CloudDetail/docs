Grafana-Beyla only supports running on kernel versions 5.8 and above. For more details, please refer to the official Grafana-Beyla documentation: https://grafana.com/docs/beyla/latest/#requirements

In APO-OneAgent, Grafana-Beyla is primarily used for monitoring Go language services. If your operating system kernel does not meet the requirements for running Beyla, you can choose to remove the Grafana-Beyla related configurations in `daemonset/apo-one-agent`, or you can configure to disable Beyla during installation.

## Deploying APO-OneAgent with Helm Charts and Disabling Grafana-Beyla
Configure the `apo-one-agent-values.yaml` file as follows:

```yaml
# apoServerIP is the ClusterIP where APO-server is located
global:
  apoServerIP: x.x.x.x # FIXME

# APO-one-agent enabled flag, default is false
odigos:
  instrumentor:
    # targetNamespace 
    # name: target namespace
    # value:
    #   enabled: inject all existing services, but do not inject newly added applications
    #   enabledFuture: inject all current and future services
    #   disabled: do not inject services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
    targetNamespace:
    - name: default
      value: enabledFuture
    - name: default2
      value: enabled
    - name: default3
      value: disabled
    # instrument-all-namespace: whether to inject all namespaces
    # equivalent to setting enabledFuture for all namespaces
    # but if ns or workload is already set to disabled, it will not be injected
    instrumentAllNamespace: false
    # force-instrument-all-namespace: whether to forcibly inject all namespaces
    # similar to instrument-all-namespace, all namespaces are set to enabledFuture
    # and all disabled settings are ignored
    forceInstrumentAllNamespace: false
grafanaBeyla:
  enabled: false
```

Execute the helm upgrade (install) command:
```bash
helm upgrade --install apo apo/apo-one-agent -n apo -f apo-one-agent-values.yaml
```
