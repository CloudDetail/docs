**APO is divided into two parts:**

- **APO-server:** The server-side component of APO.
- **APO-one-agent:** The client-side component of APO, used to collect information about monitored services and cluster/host environments in Kubernetes clusters or virtual machine setups.

This document provides a detailed guide on configuring custom Java agents for APO-one-agent. Import your Trace agent into OneAgent and leverage OneAgent's auto-injection capability to automatically inject custom Trace agents into your services. Ensure that you have [installed APO-Server](/docs/Installation/APO%20Server.md) before installing APO-one-agent.

APO-one-agent comes with default agent versions for various programming languages:
1. Java agent: [opentelemetry-java-instrumentation v2.8.0](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/tag/v2.8.0)
2. Python agent: [opentelemetry-python 1.27.0/0.48b0](https://github.com/open-telemetry/opentelemetry-python/releases/tag/v1.27.0)
3. NodeJs agent: [opentelemetry-js v1.24.1/0.51.1](https://github.com/open-telemetry/opentelemetry-js)
4. Go agent: [Grafana Beyla v1.8.4](https://github.com/grafana/beyla/releases/tag/v1.8.4)

> **Note:** Currently, only Skywalking or OpenTelemetry Java agents are recommended; replacing agents for languages other than Java is not supported.

**Process Overview:**
1. Replace the default Java agent in the APO-OneAgent auto-injection component image with your custom Java agent.
2. Push the custom-built image to your image repository.
3. Modify the deployment configuration to replace the APO-OneAgent auto-injection component image.

## How to Replace the Java Agent in APO-one-agent

For Java applications, APO-one-agent integrates the following Java agent versions by default:
- Opentelemetry v2.5.0 (default)

This document explains how to use other Java agent versions when installing APO-one-agent.

If the default integrated agent version does not meet your requirements, follow the steps below to build a custom agent image for APO-one-agent. This allows you to use other agent versions, such as Skywalking v9.2.0 or Opentelemetry v2.2.0.

### 1. Download the OneAgent Builder Template

Download link: https://apo-ce.oss-cn-hangzhou.aliyuncs.com/preload-builder.zip

The file directory structure of the template is as follows:
```bash
preload-builder
├── opentelemetry-java
│   ├── Dockerfile
│   ├── libapoinstrument.conf
│   └── opentelemetry
│       └── opentelemetry-javaagent.jar
└── skywalking-java
    ├── Dockerfile
    ├── libapoinstrument.conf
    └── skywalking-agent
        ├── ...
        └── skywalking-agent.jar
```

### 2. Replace the Agent Files
> Note: You only need to replace the agent you require; there is no need to replace both OTEL and Skywalking agents simultaneously.

#### OTEL Agent

The OTEL Java agent is provided as a single JAR package.

Replace the `opentelemetry-java/opentelemetry/opentelemetry-javaagent.jar` file in the template directory with the OTEL agent of the required version, ensuring that the filename remains unchanged.

#### Skywalking Agent

The agent files obtained from the Skywalking official website usually consist of a folder containing `skywalking-agent.jar` and its configuration files.

The recommended approach is to remove the `skywalking-java/skywalking-agent` directory from the template and replace it with the agent files downloaded from the official website.

This is because different versions of `skywalking-agent.jar` and other files may be incompatible, and a complete replacement is the safest approach.

Ensure that the filename `skywalking-java/skywalking-agent/skywalking-agent.jar` is not altered after replacement.

### 3. Building the Image

#### If using a custom OTEL agent

Use the following script to build the agent:
```bash
cd opentelemetry-java
docker build -t registry.cn-hangzhou.aliyuncs.com/originx/odigos-odiglet:latest-custom .
```

#### If using a custom Skywalking agent

Use the following script to build the agent:
```bash
cd skywalking-java
docker build -t registry.cn-hangzhou.aliyuncs.com/originx/odigos-odiglet:latest-custom .
```

### 4. Syncing the Image to the Machine

You need to push the built image to an available Harbor repository or manually sync it to the machine.

- Pushing to a Harbor repository (ensure to modify the `HARBOR_REGISTRY` in the script to your repository address):
```bash
HARBOR_REGISTRY=#modify to your repository address

docker tag registry.cn-hangzhou.aliyuncs.com/originx/odigos-odiglet $HARBOR_REGISTRY/originx/odigos-odiglet:latest-custom
docker push $HARBOR_REGISTRY/originx/odigos-odiglet:latest-custom
```

- Manually syncing to the machine:
```bash
docker save registry.cn-hangzhou.aliyuncs.com/originx/odigos-odiglet:latest-custom | gzip > apo-odiglet.tgz
```
Copy the generated file to the machine where the agent will be deployed (if in a K8s environment, copy it to each node).
On each node, execute the following command to load the image:
```bash
gunzip -c apo-odiglet.tgz | docker load 
```

### 5. Using the Newly Built Image

After completing the image push steps above, you will have an available agent image version:

1. If using Harbor, subsequently use `$HARBOR_REGISTRY/originx/odigos-odiglet:latest-custom`, ensuring `$HARBOR_REGISTRY` is modified to your repository address.
2. If manually pushed, subsequently use `registry.cn-hangzhou.aliyuncs.com/originx/odigos-odiglet:latest-custom`.

#### Kubernetes Installation of OneAgent

Refer to the [《APO OneAgent》](/docs/Installation/APO%20OneAgent.md) section on "Configuring APO-one-agent" and modify your custom image. When installing APO-one-agent, use the following parameters to specify the use of your custom image version, and modify the configuration file before installation to replace the image name and tag in the configuration.

```yaml
# apoServerIP is the ClusterIP where APO-server is located
global:
  apoServerIP: x.x.x.x # FIXME

# APO-one-agent enabled flag, default is false
odigos:
  config:
    javaAgentType: otherveriosns
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
    # similar to instrument-all-namespace, all namespaces are set to enabledFuture
    # and all disabled settings are ignored
    forceInstrumentAllNamespace: false

# If your custom probe is the skywalking version, please add this configuration
# apoOneAgent:
#   config:
#     javaAgentType: "skywalking"

odiglet:
  image:
    repository: "registry.cn-hangzhou.aliyuncs.com/originx/apo-odiglet" # FIXME
    tag: "latest-custom" # FIXME The default tag for this document is latest-custom. If you modify the tag during packaging, please sync it with your custom tag.

# Grafana-Beyla startup flag, default is false
# Beyla runs as a Go language Trace probe in OneAgent, disabled by default, requires system kernel 5.8 or higher
# For running conditions, refer to the official Grafana-Beyla documentation: https://grafana.com/docs/beyla/latest/
# grafanaBeyla:
#   enabled: true
```

Use the following command to update or install APO-OneAgent

```
helm upgrade -i apo-one-agent apo/apo-one-agent -n apo --create-namespace \
-f apo-one-agent-values.yaml
```

#### Installing OneAgent on Traditional Servers
Refer to the "Deploying APO-one-agent" section in [《APO OneAgent》](/docs/Installation/APO%20OneAgent.md) for instructions. When installing APO-one-agent, use the following parameters to specify your custom version image:
```bash
export APO_VERSION=latest
export APO_PROXY_IP=<YOUR APO-server NodeIP> # FIXME
# Add two environment variable configurations
export JAVA_AGENT_TYPE="OTHERVERSIONS-SW" # If you are using Skywalking's Java agent
# export JAVA_AGENT_TYPE="OTHERVERSIONS-OTEL" # If you are using Opentelemetry's Java agent
export APO_ODIGLET_IMAGE="Your custom APO-one-agent image full name" # FIXME

tar -zxvf apo-one-agent-compose-$APO_VERSION.tgz
cd apo-one-agent-compose/
bash deploy.sh init $APO_SERVER_IP
```

## Probe Configuration Changes (Advanced Feature)
> Note: If you do not have the need to modify configurations, you can skip this section. APO and the provided templates have already configured the basic parameters.

Most of the time, you do not need to worry about the configuration of the probes. Preload configures the necessary two parameters for the probes through certain logic:

- Service Name (OTEL_SERVICE_NAME/SW_AGENT_NAME): Generated from the jar package or main class name
- Data Sending Address (OTEL_EXPORTER_OTLP_ENDPOINT/ SW_AGENT_COLLECTOR_BACKEND_SERVICES): Maintained by the APO deployment files

All other parameters use the default configurations of the probes themselves.

If you need to modify the default parameters, you can refer to the following content to modify the corresponding `libapoinstrument.conf` file in the template. Taking the following example template as an example, each template contains three files or directories, where AAAA/BBBB are generic, meaning the file names are not restricted:

- Dockerfile: Determines which files in the preload-agent image need to be updated, usually no modification is needed
- libapoinstrument.conf: Determines the configuration when using the probe
- BBBB directory: Contains the executable files and default configurations of the custom probe
```bash
preload-builder
└── AAAA
    ├── Dockerfile
    ├── libapoinstrument.conf
    └── BBBB
        └── CCCC.jar

```
When building the image,

- All files in the `AAAA` directory will be copied to the `/etc/apo/instrumentations/custom/` directory in the image;
- All configurations in `libapoinstrument.conf` will replace the existing configurations in the image.

#### libapoinstrument.conf Configuration Explanation
Taking the configuration for OpenTelemetry as an example, the configuration file in the template is as follows:
```bash
[java]
OTEL_SERVICE_NAME={{APO_AUTO_SERVICE_NAME}}
OTEL_EXPORTER_OTLP_ENDPOINT= {{APO_COLLECTOR_GRPC_ENDPOINT}}
JAVA_TOOL_OPTIONS=-javaagent:/etc/apo/instrumentations/custom/opentelemetry/opentelemetry-javaagent.jar
JAVA_OPTS=-javaagent:/etc/apo/instrumentations/custom/opentelemetry/opentelemetry-javaagent.jar
OTEL_EXPORTER_OTLP_PROTOCOL=grpc
OTEL_TRACES_SAMPLER=always_on
OTEL_TRACES_EXPORTER=otlp
OTEL_METRICS_EXPORTER=none
OTEL_LOGS_EXPORTER=none
```
Below is an explanation of the configuration:

- [java] marks the beginning of a section, indicating that all configurations in this section will be automatically injected as environment variables when the Java program starts. Currently, four languages are supported: Java, Python, Node.js, and .NET.
- `OTEL_SERVICE_NAME={{APO_AUTO_SERVICE_NAME}}`: Since the service name needs to be dynamically generated when the application starts, the dynamic variable `{{APO_AUTO_SERVICE_NAME}}` provided by APO is used for replacement. The available dynamic parameters are as follows:
   - APO_AUTO_SERVICE_NAME: Automatically discovered service name. Currently, only Java is supported. For Python, Node.js, and .NET, the corresponding language name will be used as APO_AUTO_SERVICE_NAME.
   - APO_COLLECTOR_GRPC_ENDPOINT: The OtelCollector address of APO, which receives data in the OTLP protocol.
   - APO_COLLECTOR_HTTP_ENDPOINT: Same as above, but receives data in the OTLP HTTP protocol.
   - APO_COLLECTOR_SKYWALKING_ENDPOINT: Same as above, but receives data in the SkyWalking protocol.
- `JAVA_TOOL_OPTIONS=-javaagent:/etc/apo/instrumentations/custom/BBBB/CCCC.jar`: Since all files in the template directory will be copied to the `/etc/apo/instrumentations/custom/` directory in the image, this absolute path is used to specify the agent file.
- Other parameters are static and will be directly added as environment variables when the program starts. The following documentation provides guidance on how to use environment variables to set agent parameters.

For custom configurations of OTEL, refer to the following documentation:
[Configuration](https://opentelemetry.io/docs/zero-code/java/agent/configuration/#configuring-with-environment-variables)

For custom configurations of SkyWalking, refer to the following documentation:
[Setting Override](https://skywalking.apache.org/docs/skywalking-java/next/en/setup/service-agent/java-agent/setting-override/)
