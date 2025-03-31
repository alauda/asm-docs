---
weight: 10
title: 微服务资源描述
sourceSHA: 899df71541f10ba52d6b9391a5e8823fc9f44be143787d99c79710ab194e026e
---

本文档主要提供ServiceMesh类型微服务的资源描述。

### <span id="ms">微服务资源参考</span>

#### **YAML示例**

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

#### **顶层参数**

| 参数名称         | 必需    | 类型     | 描述                                                                                                                                                           |
| ---------------- | ------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **apiVersion**    | 是      | 字符串   | API版本，必须为`asm.alauda.io/v1beta3`。                                                                                                                      |
| **kind**          | 是      | 字符串   | 资源对象的类型，必须为`MicroService`。                                                                                                                       |
| **metadata**      | 是      | 对象     | 服务的元数据，包括服务名称（name）、命名空间（namespace）、标签（labels）及注释（annotations）。                                                               |
| **[spec](#spec)** | 是      | 对象     | 有关服务的详细信息，包括访问日志、Sidecar、OpenTelemetry Java Agent、Deployment等服务配置选项。                                                                |

#### <span id="spec">**spec参数**</span>

- <span id="dep">**deployments**</span>

  **deployments**用于指定运行服务的Deployment。Deployment必须与服务在同一命名空间中。

  | 参数名称       | 必需    | 描述                                                |
  | -------------- | ------- | ------------------------------------------------- |
  | **name**       | 是      | 与服务关联的Deployment的名称。                   |

- <span id="svc">**services**</span>

  **services**用于描述与[deployments](#dep) **一对一**关联的服务。

  **服务**必须满足以下条件：

  - 只有一个服务与服务的Deployment关联，且该服务仅与选定的Deployment相关联。

  - 服务类型为**NodePort**或**ClusterIP**。

  - 服务支持HTTP、HTTP2、gRPC和TCP协议。

  | 参数名称       | 必需    | 描述                      |
  | -------------- | ------- | ------------------------- |
  | **name**       | 是      | 服务的名称。             |

- **accessLogging**

  **accessLogging**用于控制是否收集并输出当前服务的访问日志（access log），以便记录访问当前服务的Web服务的访问日志。

  | 参数名称        | 必需    | 描述                                     |
  | --------------- | ------- | ---------------------------------------- |
  | **enabled**     | 否      | 是否收集并输出访问当前服务的Web服务的访问日志。 |

- **auth**

  **auth**用于控制是否对服务配置的黑白名单规则生效。

  | 参数名称         | 必需    | 描述                                       |
  | ---------------- | ------- | ------------------------------------------ |
  | **enabled**      | 否      | 黑白名单规则是否生效，默认情况下禁用。   |
