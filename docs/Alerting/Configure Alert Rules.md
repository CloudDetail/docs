APO integrates alert information into the guided troubleshooting interface, converting infrastructure status, network quality status, and K8s event status into alert messages, helping users quickly determine the root cause of issues within the same interface. By default, APO enables a set of alert rules, which can be viewed by clicking `Alerts Rule` in the guided troubleshooting interface.

To modify alert rules, enter the following command in the server cluster:
```
kubectl edit cm apo-victoria-metrics-alert-server-alert-rules-config -n apo
```
Refer to the [Prometheus Alerting Rules Configuration Documentation](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/) for modifications. In the future, APO will provide a user-friendly interface to simplify the configuration process.
