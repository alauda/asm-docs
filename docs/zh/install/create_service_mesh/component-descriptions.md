---
weight: 10
title: 服务网格组件描述
sourceSHA: 3c7459b01c37148f3962e0ac741bf9a8235ec206e60d8997222a360d90d34659
---

本文档提供了服务网格的各种组件及其在平台中角色的简要概述。

## 涉及的开源解决方案

- **Istio**: 一种开源服务网格解决方案，提供流量管理、安全性和可观察性功能。
- **OpenTelemetry**: 一种开源可观察性解决方案，支持广泛适用的代码级监控。
- **Jaeger**: 一种开源分布式追踪解决方案。
- **Flagger**: 一种开源渐进式交付工具，支持包括金丝雀发布、A/B 测试和蓝绿部署在内的各种部署策略。

## 组件描述

<style>
  .nowrap {
    white-space: nowrap;
  }
</style>

**控制平面组件**

| 组件名称                                   | 描述                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Istio**                                  |                                                                                                                                                                                                                                                                                                                                                                                               |
| <span class="nowrap">istiod                | Istio 的控制平面组件，其名称区分不同主要版本的 Istio。                                                                                                                                                                                                                                                                                                                                 |
| <span class="nowrap">istio-cni             | Istio CNI 节点代理，用于配置网格内 Pods 的流量重定向。此组件在默认情况下不被部署，除非使用 OpenShift 集群。有关部署的详细信息，请参阅 \[启用 Istio CNI]\({{< relref "mesh-u-servicemesh/create-servicemesh/istio-cni.md" >}})。                                                                                                                                                     |
| <span class="nowrap">tier2-gateway         | Tier2 网关与 Tier1 网关配合，提供高级流量管理功能，如灰度发布和网格中入口流量的服务路由。<br>有关 Tier1 和 Tier2 的更多信息，请参见 \[网格中入口流量的流量路由]\({{< relref "mesh-u-servicemesh/managing-traffic/1ingress-gateway/1understand-ingress-gateway.md#tier2" >}})。                                                                 |
| <span class="nowrap">istio-eastwestgateway | Istio 中用于多集群服务网格通信的东西网关。其主要功能是确保不同集群中的服务可以相互通信。此组件仅在 `multi-network` 和 `multi-cluster` 服务网格中部署。                                                                                                                                                                                                                        |
| <span class="nowrap">asm-core              | 提供 Istio 的全局速率限制的组件。此组件默认部署，但仅在服务网格与 Redis 集成时才会起作用。                                                                                                                                                                                                                                                                                               |
| **OpenTelemetry**                          |                                                                                                                                                                                                                                                                                                                                                                                               |
| <span class="nowrap">asm-otel-collector    | OpenTelemetry 解决方案的组件，用于接收、处理和导出可观察性数据。支持开源数据格式，如 Jaeger 和 Prometheus。追踪数据发送至 Jaeger，而指标数据发送至 Prometheus 或 VictoriaMetrics。                                                                                                                                                                                                               |
| <span class="nowrap">asm-otel-collector-lb | 为 asm-otel-collector 组件提供负载均衡。通过此组件收集到的 OTel Java Agent 的可观察性数据会转发至 asm-otel-collector。                                                                                                                                                                                                                                                                                                     |
| **Jaeger**                                 |                                                                                                                                                                                                                                                                                                                                                                                               |
| <span class="nowrap">jaeger-prod-collector | Jaeger 的收集组件，从侧车和 asm-otel-collector 收集分布式追踪数据，并与 Elasticsearch 接口以存储追踪数据。                                                                                                                                                                                                                                                                                          |
| <span class="nowrap">jaeger-prod-query     | Jaeger 的数据查询组件，从 Elasticsearch 查询追踪数据。                                                                                                                                                                                                                                                                                                                                  |
| **Flagger**                                |                                                                                                                                                                                                                                                                                                                                                                                               |
| <span class="nowrap">flagger               | 提供灰度发布功能，并与 Istio 集成以实施服务的渐进式交付策略。                                                                                                                                                                                                                                                                                                                                |
| **其他**                                   |                                                                                                                                                                                                                                                                                                                                                                                               |
| <span class="nowrap">asm-controller        | 管理并协调服务网格内的各种资源。                                                                                                                                                                                                                                                                                                                                                           |

**数据平面组件**

| 组件名称               | 描述                                                                                                                                                                                                                                                                |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 入口网关              | 运行在网格边缘的独立 Envoy 代理。它允许配置公共端口和协议，以指定允许进入网格的流量。                                                                                                                                                                              |
| 出口网关              | 运行在网格边缘的独立 Envoy 代理。它允许监控、日志记录和对离开网格的流量进行安全策略的执行。                                                                                                                                                                             |
| 侧车                  | 通常与应用程序容器部署在同一 Pod 中。它处理来自应用程序的网络请求，包括流量管理、安全策略执行、负载均衡和服务发现。                                                                                                                                                      |
| OpenTelemetry Java Agent | 一种用于在 Java 应用程序中收集分布式追踪和指标数据的自动化工具。通过注入 OpenTelemetry Java Agent，可以在不修改应用程序代码的情况下进行请求路径的分布式追踪和性能指标的收集。                                                                                |

## CPU 和内存分配

本节概述了集群中服务网格组件的最低推荐计算资源。

每个组件的 CPU 和内存分配是 \[可配置的]\({{< relref "mesh-u-servicemesh/create-servicemesh/create-servicemesh.md#config\_com" >}})。

在创建服务网格之前，请确保集群节点具有足够的 CPU 和内存来运行所有的服务网格组件。

**重要说明：**

- 在生产环境中，建议不为 istiod 设置限制，这意味着 CPU 和内存不受限制。
- 对于大型部署，强烈建议使用节点选择器将基础设施放置在集群中的专用节点上，以便为每个 Istio 组件提供支持。

下表总结了每个服务网格组件的最低资源请求和限制的推荐值。

在 Kubernetes 中，资源请求指示工作负载不会在某节点上部署，除非该节点至少具有规定数量的可用内存和 CPU。如果工作负载超过 CPU 或内存限制，可能会被终止或从节点中驱逐。有关管理容器资源限制的更多信息，请参见 [Kubernetes 文档](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/)。

| 组件                 | CPU - 请求   | 内存 - 请求   | CPU - 限制   | 内存 - 限制   |
| --------------------- | ------------- | -------------- | ------------- | -------------- |
| **Istio**             |               |                |               |                |
| istiod                | 500m          | 512Mi          | 2000m         | 2048Mi         |
| 入口网关             | 250m          | 128Mi          | 2000m         | 1024Mi         |
| 出口网关             | 250m          | 128Mi          | 2000m         | 1024Mi         |
| 侧车                 | 100m          | 128Mi          | 500m          | 512Mi          |
| tier2-网关           | 250m          | 128Mi          | 2000m         | 1024Mi         |
| istio-eastwestgateway | 250m          | 128Mi          | 2000m         | 1024Mi         |
| asm-core              | 250m          | 128Mi          | 1000m         | 512Mi          |
| **OpenTelemetry**     |               |                |               |                |
| asm-otel-collector    | 250m          | 512Mi          | 2000m         | 1Gi            |
| asm-otel-collector-lb | 250m          | 512Mi          | 1000m         | 1Gi            |
| **Jaeger**            |               |                |               |                |
| jaeger-prod-collector | 250m          | 512Mi          | 3000m         | 512Mi          |
| jaeger-prod-query     | 250m          | 512Mi          | 1000m         | 512Mi          |
| **Flagger**           |               |                |               |                |
| flagger               | 250m          | 128Mi          | 1000m         | 512Mi          |
| **其他**             |               |                |               |                |
| asm-controller        | 250m          | 512Mi          | 1000m         | 512Mi          |
