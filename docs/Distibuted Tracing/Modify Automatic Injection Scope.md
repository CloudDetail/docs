This document is used to customize the auto-instrumentation scope of apo-one-agent in Kubernetes environments. Please refer to the corresponding section in your apo-one-agent deployment documentation for configuration. By default, apo-one-agent monitors all components except those in the apo and Kubernetes system namespaces.

## Using the "Quick Start" Guide for Deployment
Create a file named `apo-values.yaml` and define it according to your requirements. Modify the deployment command to read this `apo-values.yaml` file. The modified deployment command is as follows:
```
helm install apo apo/apo -n apo --create-namespace \
--set apo-one-agent.enabled=true \
-f apo-values.yaml
```

### Example 1 - apo-values.yaml: Adding Specific Namespaces to the Blacklist
This example configures apo-one-agent to add the `default` and `test` namespaces to the blacklist during instrumentation. It sets `instrumentAllNamespace` to `true`, enabling instrumentation in all other namespaces.
```
apo-one-agent:
  odigos:
    instrumentor:
      # targetNamespace 
      # name: Target namespace
      # value:
      #   enabled: Instrument all existing services but not future applications
      #   enabledFuture: Instrument both existing and future services
      #   disabled: Do not instrument services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
      targetNamespace:
      - name: default
        value: disable
      - name: test
        value: disable
      # instrument-all-namespace: Whether to instrument all namespaces
      # Equivalent to setting enabledFuture for all namespaces
      # However, if a namespace or workload is set to disabled, it will not be instrumented
      instrumentAllNamespace: true
      # force-instrument-all-namespace: Whether to force instrumentation in all namespaces
      # Similar to instrument-all-namespace, sets enabledFuture for all namespaces
      # Ignores all disabled settings
      forceInstrumentAllNamespace: false
```

### Example 2 - apo-values.yaml: Adding Specific Namespaces to the Whitelist
This example configures apo-one-agent to add the `default` and `test` namespaces to the whitelist during instrumentation. It sets `instrumentAllNamespace` to `false`, preventing instrumentation in all other namespaces.
```
apo-one-agent:
  odigos:
    instrumentor:
      # targetNamespace 
      # name: Target namespace
      # value:
      #   enabled: Instrument all existing services but not future applications
      #   enabledFuture: Instrument both existing and future services
      #   disabled: Do not instrument services in the specified namespace, used to ignore specific namespaces when instrument-all-namespace is enabled
      targetNamespace:
      - name: default
        value: enabledFuture
      - name: test
        value: enabledFuture
      # instrument-all-namespace: Whether to instrument all namespaces
      # Equivalent to setting enabledFuture for all namespaces
      # However, if a namespace or workload is set to disabled, it will not be instrumented
      instrumentAllNamespace: false
      # force-instrument-all-namespace: Whether to force instrumentation in all namespaces
      # Similar to instrument-all-namespace, sets enabledFuture for all namespaces
      # Ignores all disabled settings
      forceInstrumentAllNamespace: false
```
