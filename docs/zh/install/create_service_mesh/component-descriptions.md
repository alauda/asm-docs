---
weight: 10
title: 服务网格组件描述
sourceSHA: 3c7459b01c37148f3962e0ac741bf9a8235ec206e60d8997222a360d90d34659
---

本文档提供了服务网格各个组件及其在平台中角色的简要概述。

## 相关开源解决方案

- **Istio**：一个开放源代码的服务网格解决方案，提供流量管理、安全性和可观察性功能。
- **OpenTelemetry**：一个开源的可观察性解决方案，支持广泛适用的代码级仪表化。
- **Jaeger**：一个开源的分布式追踪解决方案。
- **Flagger**：一个开源的渐进式交付工具，支持各种部署策略，包括金丝雀发布、A/B 测试和蓝绿部署。

## 组件描述

<style>
  .nowrap {
    white-space: nowrap;
  }
</style>

**控制平面组件**

| 组件名称                                    | 描述                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Istio**                                   |                                                                                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">istiod                 | Istio 的控制平面组件，其名称区分了不同的主要版本。                                                                                                                                                                                                                                                                                                                                       |
| <span class="nowrap">istio-cni              | Istio CNI 节点代理，用于配置服务网格中 Pods 的流量重定向。该组件默认情况下不部署，除非使用 OpenShift 集群。有关部署详细信息，请参阅 \[启用 Istio CNI]\({{< relref "mesh-u-servicemesh/create-servicemesh/istio-cni.md" >}})。                                                                                                                                                   |
| <span class="nowrap">tier2-gateway          | Tier2 网关与 Tier1 网关结合提供高级流量管理功能，例如灰度发布和服务路由，用于服务网格中的入口流量。<br>有关 Tier1 和 Tier2 的更多信息，请参阅 \[服务网格中入口流量的流量路由]\({{< relref "mesh-u-servicemesh/managing-traffic/1ingress-gateway/1understand-ingress-gateway.md#tier2" >}})。                                             |
| <span class="nowrap">istio-eastwestgateway  | Istio 中的东西网关，用于多集群服务网格通信。其主要功能是确保不同集群中的服务能够相互通信。该组件仅在 `multi-network` 和 `multi-cluster` 服务网格中部署。                                                                                                                                                              |
| <span class="nowrap">asm-core               | 为 Istio 提供全局速率限制的组件。默认情况下部署，但仅在服务网格与 Redis 集成时有效。                                                                                                                                                                                                                                                                  |
| **OpenTelemetry**                           |                                                                                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">asm-otel-collector     | OpenTelemetry 解决方案的一部分，用于接收、处理和导出可观察性数据。支持 Jaeger 和 Prometheus 等开源数据格式。追踪数据发送到 Jaeger，而指标数据则发送到 Prometheus 或 VictoriaMetrics。                                                                                                                                                                  |
| <span class="nowrap">asm-otel-collector-lb  | 为 asm-otel-collector 组件提供负载均衡。OTel Java Agent 收集的可观察性数据通过该组件转发到 asm-otel-collector。                                                                                                                                                                                                                                                                     |
| **Jaeger**                                  |                                                                                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">jaeger-prod-collector  | Jaeger 的收集器组件，从 sidecar 和 asm-otel-collector 收集分布式追踪数据，并与 Elasticsearch 接口以存储追踪数据。                                                                                                                                                                                                                                                               |
| <span class="nowrap">jaeger-prod-query      | Jaeger 的数据查询组件，从 Elasticsearch 查询追踪数据。                                                                                                                                                                                                                                                                                                                                     |
| **Flagger**                                 |                                                                                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">flagger                | 提供灰度发布功能，并与 Istio 集成以实施服务的渐进式交付策略。                                                                                                                                                                                                                                                                                                                                  |
| **其他**                                    |                                                                                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">asm-controller         | 管理和协调服务网格中的各种资源。                                                                                                                                                                                                                                                                                                                                                               |

**数据平面组件**

| 组件名称            | 描述                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 入口网关           | 在网格边缘运行的独立 Envoy 代理。它允许配置公共端口和协议，以指定哪些流量被允许进入网格。                                                                                                                                                                                                                           |
| 出口网关           | 在网格边缘运行的独立 Envoy 代理。它允许监控、记录和执行离开网格流量的安全策略。                                                                                                                                                                                                                                              |
| sidecar            | 通常与应用程序容器部署在同一 Pod 中。它处理来自应用程序的网络请求，包括流量管理、安全政策执行、负载均衡和服务发现。                                                                                                                                                                                                 |
| OpenTelemetry Java Agent | 一个自动化工具，用于收集 Java 应用程序中的分布式追踪和指标数据。通过注入 OpenTelemetry Java Agent，可以在不修改应用程序代码的情况下完成请求路径的分布式追踪和性能指标的采集。                                                                                                 |

## CPU 和内存分配

本节概述了集群中服务网格组件的最低推荐计算资源。

各组件的 CPU 和内存分配为 \[可配置]\({{< relref "mesh-u-servicemesh/create-servicemesh/create-servicemesh.md#config\_com" >}})。

在创建服务网格之前，请确保集群节点有足够的 CPU 和内存以运行所有服务网格组件。

**重要注意事项：**

- 在生产环境中，建议不为 istiod 设置限制，意味着 CPU 和内存不受限制。
- 对于大规模部署，强烈建议使用节点选择器将基础设施部署到集群中的专用节点，以便为每个 Istio 组件提供服务。

下表总结了每个服务网格组件的推荐最小资源请求和限制的 CPU 和内存。

在 Kubernetes 中，资源请求表示工作负载不会在节点上部署，除非该节点至少具有指定数量的可用内存和 CPU。如果工作负载超过 CPU 或内存限制，可能会被终止或驱逐出节点。有关管理容器资源限制的更多信息，请参阅 [Kubernetes 文档](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/)。

| 组件                | CPU - 请求 | 内存 - 请求 | CPU - 限制 | 内存 - 限制 |
| ------------------ | ---------- | ----------- | ----------- | ------------ |
| **Istio**          |            |             |             |              |
| istiod             | 500m       | 512Mi       | 2000m       | 2048Mi       |
| 入口网关          | 250m       | 128Mi       | 2000m       | 1024Mi       |
| 出口网关          | 250m       | 128Mi       | 2000m       | 1024Mi       |
| sidecar            | 100m       | 128Mi       | 500m        | 512Mi        |
| tier2-gateway      | 250m       | 128Mi       | 2000m       | 1024Mi       |
| istio-eastwestgateway | 250m    | 128Mi       | 2000m       | 1024Mi       |
| asm-core           | 250m       | 128Mi       | 1000m       | 512Mi        |
| **OpenTelemetry**  |            |             |             |              |
| asm-otel-collector  | 250m      | 512Mi       | 2000m       | 1Gi          |
| asm-otel-collector-lb | 250m   | 512Mi       | 1000m       | 1Gi          |
| **Jaeger**         |            |             |             |              |
| jaeger-prod-collector | 250m   | 512Mi       | 3000m       | 512Mi        |
| jaeger-prod-query  | 250m       | 512Mi       | 1000m       | 512Mi        |
| **Flagger**        |            |             |             |              |
| flagger            | 250m       | 128Mi       | 1000m       | 512Mi        |
| **其他**           |            |             |             |              |
| asm-controller     | 250m       | 512Mi       | 1000m       | 512Mi        |
