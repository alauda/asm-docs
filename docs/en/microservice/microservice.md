---
weight: 10
title: Microservice Resource Description
---


This document primarily provides a description of the resources for ServiceMesh type Microservices.

### <span id="ms">MicroService Resource Reference</span>

#### **YAML Example**

```yaml
apiVersion: asm.alauda.io/v1beta3
kind: MicroService
metadata:
  labels:
    app.cpaas.io/microservice-type: service-mesh
  name: xqren-s1
  namespace: xqren-ovn
spec:
  accessLogging:
    enabled: true
  auth:
    enabled: true
  deployments:
    - name: asm-test-image
  otelJavaAgent:
    enabled: false
  services:
    - name: xqren-s1
  sidecar:
    enabled: true
    cpuLimit: '0.1'
    memoryLimit: 256Mi
    envoyLogLevel: warning
```

#### **Top-Level Parameters**

| Parameter Name                 | Required | Type     | Description |
|------------------|----------|----------|-------------|
| **apiVersion**             | Yes       | string   | API version, must be `asm.alauda.io/v1beta3`. |
| **kind**                    | Yes       | string   | Type of resource object, must be `MicroService`. |
| **metadata**                 | Yes       | object   | Metadata of the service, including the service name (name), namespace (namespace), labels (labels), and annotations (annotations).|
| **[spec](#spec)**                     | Yes       | object   | Detailed information about the service, including service configuration options such as access logging, Sidecar, OpenTelemetry Java Agent, Deployment, etc. |



#### <span id="spec">**spec Parameters**</span>

* <span id="dep">**deployments**</span>

  **deployments** is used to specify the Deployment running the service. The Deployment must be in the same namespace as the service.

  | Parameter Name | Required | Description                                             |
    |----------------|----------|---------------------------------------------------------|
  | **name**       | Yes      | The name of the Deployment associated with the service. |

* <span id="svc">**services**</span>

  **services** is used to describe the service associated **one-to-one** with the [deployments](#dep).

  **Service** must meet the following conditions:

  * Only one service is associated with the service's Deployment, and the service is only associated with the selected Deployment.

  * The service type is **NodePort** or **ClusterIP**.

  * The service supports HTTP, HTTP2, gRPC, and TCP protocols.

  | Parameter Name | Required | Description              |
     |----------------|----------|--------------------------|
  | **name**       | Yes      | The name of the service. |

* **accessLogging**

  **accessLogging** is used to control whether to collect and output the access logs (access log) of web services accessing the current service in the logs.

  | Parameter Name   | Required |  Description |
      |------------------|----------|--------------|
  | **enabled** | No     |  Whether to collect and output the access logs (access log) of web services accessing the current service. |

* **auth**

  **auth** is used to control whether the black and white list rules configured for the service take effect.

  | Parameter Name    | Required |  Description |
      |-------------------|----------|--------------|
  | **enabled**     | No       | Whether the black and white list rules take effect, disabled by default. |
