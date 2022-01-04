---
title: Tracing
keywords: ["Tracing"]
description: Tracing access
---

This article introduces how to use the `Apache ShenYu Agent Tracing`.

`Apache ShenYu` uses `java agent` and `bytecode enhancement` technology to achieve seamless embedding, so that users can access third-party observability systems without introducing dependencies, and obtain Traces, Metrics and Logging.

## Catalog Structure

```text
.
├── conf
│   ├── logback.xml
│   ├── shenyu-agent.yaml
│   └── tracing-point.yaml
├── plugins
│   ├── shenyu-agent-plugin-tracing-common-${latest.release.version}-SNAPSHOT.jar
│   ├── shenyu-agent-plugin-tracing-jaeger-${latest.release.version}-SNAPSHOT.jar
│   ├── shenyu-agent-plugin-tracing-opentelemetry-${latest.release.version}-SNAPSHOT.jar
│   └── shenyu-agent-plugin-tracing-zipkin-${latest.release.version}-SNAPSHOT.jar
└── shenyu-agent.jar
```

## Edit shenyu-agent.yaml

```yaml
appName: shenyu-agent
supports:
  tracing:
    - jaeger
    - opentelemetry
    - zipkin

plugins:
  tracing:
    jaeger:
      host: "localhost"
      port: 5775
      props:
        SERVICE_NAME: "shenyu-agent"
        JAEGER_SAMPLER_TYPE: "const"
        JAEGER_SAMPLER_PARAM: "1"
    zipkin:
      host: 
      port: 
      props:
    opentelemetry:
      props:
        otel.traces.exporter: jaeger #zipkin #otlp
        otel.resource.attributes: "service.name=shenyu-agent"
        otel.exporter.jaeger.endpoint: "http://localhost:14250/api/traces"
```

- Select the plugin to be used in `supports`
- Configure the parameters of the plug-in in `plugins`. The specific usage of each plug-in props parameter is shown in the following tables:

#### jaeger

| Name                 |  Type  | Default      | Description                             |
| :------------------- | :----: | :----------- | :-------------------------------------- |
| SERVICE_NAME         | String | shenyu-agent | The name displayed in the traces system |
| JAEGER_SAMPLER_TYPE  | String | const        | Jaeger sample rate type                 |
| JAEGER_SAMPLER_PARAM | String | 1            | Jaeger sample rate parameters           |

#### opentelemetry

| Name                     |  Type  | Default                   | Description                                                  |
| :----------------------- | :----: | :------------------------ | :----------------------------------------------------------- |
| otel.traces.exporter     | String | jaeger                    | Traces exporter type, if not filled in, the default is otlp  |
| otel.resource.attributes | String | service.name=shenyu-agent | otel resource attributes, if you fill in more than one, they can be separated by commas |

The SDK used by the opentelemetry plugin is initialized based on `opentelemetry-sdk-extension-autoconfigure`. For more usage, please refer to [OpenTelemetry SDK AutoConfiguration Instructions](https://github.com/open-telemetry/opentelemetry-java/tree/v1.9.1/sdk-extensions/autoconfigure#opentelemetry-sdk-autoconfigure)

#### zipkin