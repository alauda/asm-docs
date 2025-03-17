---
weight: 10
title: Description of Mesh Components
---

This document provides a brief overview of the various components of the service mesh and their roles within the platform.

## Involved Open Source Solutions

- **Istio**: An open-source service mesh solution providing traffic management, security, and observability features.
- **OpenTelemetry**: An open-source observability solution that supports code-level instrumentation with broad applicability.
- **Jaeger**: An open-source distributed tracing solution.
- **Flagger**: An open-source progressive delivery tool that supports various deployment strategies, including Canary, A/B Testing, and Blue/Green.

## Component Description

<style>
  .nowrap {
    white-space: nowrap;
  }
</style>

**Control Plane Components**

| Component Name | Description |
| -------------- | ----------- |
| **Istio** | |
| <span class="nowrap">istiod | The control plane component of Istio, with its name distinguishing different major versions of Istio. |
| <span class="nowrap">istio-cni | Istio CNI node agent used for configuring traffic redirection for Pods within the mesh. This component is not deployed by default unless using OpenShift clusters. For deployment details, see [Enable Istio CNI]({{< relref "mesh-u-servicemesh/create-servicemesh/istio-cni.md" >}}). |
| <span class="nowrap">tier2-gateway | The Tier2 gateway, in conjunction with the Tier1 gateway, provides advanced traffic management features like gray releases and service routing for ingress traffic in the mesh. <br> For more information on Tier1 and Tier2, see [Traffic Routing for Ingress Traffic in the Mesh]({{< relref "mesh-u-servicemesh/managing-traffic/1ingress-gateway/1understand-ingress-gateway.md#tier2" >}}). |
| <span class="nowrap">istio-eastwestgateway | The east-west gateway in Istio for multi-cluster service mesh communication. Its primary function is to ensure that services in different clusters can communicate with each other. This component is deployed only in `multi-network` and `multi-cluster` service meshes. |
| <span class="nowrap">asm-core | Component providing global rate limiting for Istio. It is deployed by default but only functions when the service mesh is integrated with Redis. |
| **OpenTelemetry** | |
| <span class="nowrap">asm-otel-collector | Component of the OpenTelemetry solution for receiving, processing, and exporting observability data. Supports open-source data formats like Jaeger and Prometheus. Trace data is sent to Jaeger, while Metrics data is sent to Prometheus or VictoriaMetrics. |
| <span class="nowrap">asm-otel-collector-lb | Provides load balancing for the asm-otel-collector component. Observability data collected by the OTel Java Agent is forwarded to the asm-otel-collector through this component. |
| **Jaeger** | |
| <span class="nowrap">jaeger-prod-collector | The collector component of Jaeger, collecting distributed tracing data from sidecars and asm-otel-collector, and interfacing with Elasticsearch to store trace data. |
| <span class="nowrap">jaeger-prod-query | The data query component of Jaeger, querying trace data from Elasticsearch. |
| **Flagger** | |
| <span class="nowrap">flagger | Provides gray release functionality and integrates with Istio to implement progressive delivery strategies for services. |
| **Others** | |
| <span class="nowrap">asm-controller | Manages and coordinates various resources within the service mesh. |

**Data Plane Components**

| Component Name | Description |
| -------------- | ----------- |
| Ingress Gateway | An independent Envoy proxy running at the edge of the mesh. It allows configuration of public ports and protocols to specify which traffic is allowed into the mesh. |
| Egress Gateway | An independent Envoy proxy running at the edge of the mesh. It allows monitoring, logging, and enforcement of security policies for traffic leaving the mesh. |
| Sidecar | Typically deployed in the same Pod as the application container. It handles network requests from the application, including traffic management, security policy enforcement, load balancing, and service discovery. |
| OpenTelemetry Java Agent | An automated tool for collecting distributed tracing and metrics data within Java applications. By injecting the OpenTelemetry Java Agent, distributed tracing of request paths and collection of performance metrics can be done without modifying the application code. |

## CPU and Memory Allocation

This section outlines the minimum recommended computing resources for service mesh components in the cluster.

CPU and memory allocation for each component are [configurable]({{< relref "mesh-u-servicemesh/create-servicemesh/create-servicemesh.md#config_com" >}}).

Before creating a service mesh, ensure that the cluster nodes have sufficient CPU and memory to run all service mesh components.

**Important Note:**

- In production environments, it is recommended not to set limits for istiod, meaning CPU and memory are unrestricted.
- For large deployments, it is strongly recommended to use node selectors to place infrastructure on dedicated nodes in the cluster for each Istio component.

The table below summarizes the recommended minimum resource requests and limits for CPU and memory for each service mesh component.

In Kubernetes, resource requests indicate that a workload will not be deployed on a node unless that node has at least the specified amount of available memory and CPU. If the workload exceeds CPU or memory limits, it may be terminated or evicted from the node. For more information on managing container resource limits, see the [Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/).

| Component | CPU - Request | Memory - Request | CPU - Limit | Memory - Limit |
| --------- | ------------- | ----------------- | ----------- | --------------- |
| **Istio** | | | | |
| istiod | 500m | 512Mi | 2000m | 2048Mi |
| ingress gateway | 250m | 128Mi | 2000m | 1024Mi |
| egress gateway | 250m | 128Mi | 2000m | 1024Mi |
| sidecar | 100m | 128Mi | 500m | 512Mi |
| tier2-gateway | 250m | 128Mi | 2000m | 1024Mi |
| istio-eastwestgateway | 250m | 128Mi | 2000m | 1024Mi |
| asm-core | 250m | 128Mi | 1000m | 512Mi |
| **OpenTelemetry** | | | | |
| asm-otel-collector | 250m | 512Mi | 2000m | 1Gi |
| asm-otel-collector-lb | 250m | 512Mi | 1000m | 1Gi |
| **Jaeger** | | | | |
| jaeger-prod-collector | 250m | 512Mi | 3000m | 512Mi |
| jaeger-prod-query | 250m | 512Mi | 1000m | 512Mi |
| **Flagger** | | | | |
| flagger | 250m | 128Mi | 1000m | 512Mi |
| **Others** | | | | |
| asm-controller | 250m | 512Mi | 1000m | 512Mi |
