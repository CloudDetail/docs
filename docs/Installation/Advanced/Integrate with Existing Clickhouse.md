This document provides recommendations for deploying APO in a production environment.
## 1. Configuring APO-Server to Use a Managed Single-Node Clickhouse

### Preparations
- The Clickhouse version should be at least 22.8 or higher.
- The database `apo` needs to be created in advance, and user permissions should be granted.
- This document uses the username `apo-user` and password `Apo@123456` as examples.
- The Clickhouse instance should be running and not started simultaneously with APO-Server.
### Configuring the APO-Server Deployment File
Create a file named `apo-values.yaml` and define it according to your requirements. Configure the settings below and mount the configuration during the deployment phase.
```yaml
# host         : Access address for the managed Clickhouse
# nativePort   : Native access port for the managed Clickhouse
# httpPort     : HTTP access port for the managed Clickhouse
# username     : Username for accessing the managed Clickhouse, enclosed in double quotes
# password     : Password for accessing the managed Clickhouse, enclosed in double quotes
# database     : Database name for accessing the managed Clickhouse, no need to modify unless necessary, enclosed in double quotes
global:
  clickhouse:
    host: "apo-clickhouse-svc" # FIXME
    nativePort: 9000 # FIXME
    httpPort: 8123 # FIXME
    username: "apo-user" # FIXME
    password: "Apo@123456" # FIXME
    database: "apo"

## Configuring APO Components
# APO-Server Configuration
# Configure APO-Server not to create a Clickhouse instance
altinity-clickhouse-operator:
  enabled: false
# Persistence configuration, recommended to enable, default is false
# If persistence is enabled, PVs need to be created for victoria-metrics and apo-backend based on PVC
victoria-metrics-single:
  server:
    persistentVolume:
      enabled: true
apo-backend:
  persistence: 
    enabled: true
```

Once the configuration is complete, return to the [Install APO-Server](/docs/Installation/APO%20Server.md) document to continue with the deployment.
### Verification
After the APO-Server is successfully deployed, the `apo-collector` component will automatically create the following tables in the `apo` database, indicating that the Clickhouse configuration is successful.
```yaml
agent_log
alert_event
error_propagation
...
service_relation
service_topology
slow_report
span_trace
```

## 2. Configuring APO-Server to Use a Managed Clickhouse Cluster

### Preparation
- Clickhouse version must be at least 22.8 or above.
- The database `apo` needs to be created in advance, and the user should be authorized.
- This document uses the username `apo-user`, password `Apo@123456`, and Clickhouse cluster name `ck_clickhouse` as examples.
- The Clickhouse instance needs to be running and not started simultaneously with APO-Server.
### Configuring the APO-Server Deployment File
Create the file `apo-values.yaml` and define it according to your needs. Configure the settings below and mount the configuration during the deployment phase.
```yaml
# host         : Hosted Clickhouse access address
# nativePort   : Hosted Clickhouse Native access port
# httpPort     : Hosted Clickhouse HTTP access port
# username     : Hosted Clickhouse access username, requires double quotes
# password     : Hosted Clickhouse access password, requires double quotes
# database     : Hosted Clickhouse access database name, no need to modify unless necessary, requires double quotes
# clusterName  : Hosted Clickhouse cluster name, requires double quotes
# replication  : Used to configure whether to use replication when creating tables in Clickhouse cluster mode, default is false, requires double quotes
global:
  clickhouse:
    host: "apo-clickhouse-svc"     # FIXME
    nativePort: 9000               # FIXME
    httpPort: 8123                 # FIXME
    username: "apo-user"           # FIXME
    password: "Apo@123456"         # FIXME
    database: "apo"
    clusterName: "ck_clickhouse"   # FIXME
    replication: "false"

## Configuring APO Components
# APO-Server Configuration
# Configure APO-Server not to create a Clickhouse instance
altinity-clickhouse-operator:
  enabled: false
# Persistence configuration, recommended to enable, default is false
# If persistence is configured, PV needs to be created for victoria-metrics and apo-backend according to PVC
victoria-metrics-single:
  server:
    persistentVolume:
      enabled: true
apo-backend:
  persistence: 
    enabled: true
```

After completing the configuration, return to the [Install APO-Server](/docs/Installation/APO%20Server.md) document to continue with the deployment.
### Verification
After the APO-Server is successfully deployed, the `apo-collector` component will automatically create the following tables in the `apo` database, indicating that the Clickhouse configuration is successful.
```yaml
agent_log
agent_log_local
alert_event
alert_event_local
error_propagation
error_propagation_local
...
slow_report
slow_report_local
span_trace
span_trace_local 
```
