---
weight: 5
title: Mesh Deployment Model
---

Understanding the deployment models of a service mesh is crucial for its effective use. The deployment models mainly include single-cluster service mesh and multi-cluster service mesh. The following sections provide a detailed explanation of these two deployment models.

## Single-Cluster Service Mesh

### Overview

A single-cluster service mesh is deployed within a single Kubernetes cluster, managing and governing all microservices within that cluster. This model is suitable for scenarios where services are primarily concentrated within one cluster, without the need for cross-cluster or cross-region connectivity.

### Applicable Scenarios

- Microservices are only deployed within a single Kubernetes cluster.
- No need to consider cross-cluster network connections and resource sharing.
- Simplified deployment and management, reducing complexity and operational costs.

### Advantages

- Relatively simple deployment and management, as only one cluster needs to be configured.
- High network performance since all communications occur within the same cluster.
- Centralized management and monitoring, making it easier to troubleshoot issues.

### Disadvantages

- Single point of failure: If the cluster encounters issues, all services will be affected.
- Limited scalability: Cross-cluster service governance and traffic management are not feasible.

## Multi-Cluster Service Mesh

### Overview

A multi-cluster service mesh is deployed across multiple Kubernetes clusters, enabling cross-cluster microservice governance. This model is suitable for scenarios that require service deployment across multiple clusters or have cross-region disaster recovery needs.

### Applicable Scenarios

- Microservices are distributed across multiple Kubernetes clusters.
- High availability and disaster recovery capabilities across regions are needed.
- Desire to achieve cross-cluster traffic management and service governance.

### Advantages

- High availability: Cross-cluster deployment avoids single points of failure and enhances service reliability.
- Strong scalability: Clusters can be added or removed based on business needs, allowing flexible resource management.
- Cross-region disaster recovery: Clusters deployed in different regions provide geographical disaster recovery and data redundancy.

### Disadvantages

- High deployment and management complexity, requiring consideration of cross-cluster network connections and configurations.
- Network performance may be impacted, with latency and bandwidth limitations in cross-cluster communication needing optimization.
- Stronger monitoring and operational capabilities are required to ensure the stability of cross-cluster services.

## Conclusion

When choosing a service mesh deployment model, the specific business needs and application scenarios should be considered. A single-cluster service mesh is suitable for simple application scenarios within a single cluster, while a multi-cluster service mesh offers higher flexibility and reliability for more complex scenarios that require cross-cluster and cross-region capabilities. With an understanding of these two deployment models, you can find detailed operational guides and parameter descriptions in the subsequent chapters to better configure and manage your service mesh.
