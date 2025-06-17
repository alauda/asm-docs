---
weight: 5
title: Mesh Deployment Model
---

Understanding the deployment models of a service mesh is crucial for its effective use. The deployment models mainly include single-cluster service mesh and multi-cluster service mesh. The following sections provide a detailed explanation of these two deployment models.

## Single-Cluster Service Mesh

### Overview

A single-cluster service mesh is deployed within a single Kubernetes cluster, managing and governing all microservices within that cluster. This model is suitable for scenarios where services are primarily concentrated within one cluster, without the need for cross-cluster or cross-region connectivity.

## Multi-Cluster Service Mesh

### Overview

A multi-cluster service mesh is deployed across multiple Kubernetes clusters, enabling cross-cluster microservice governance. This model is suitable for scenarios that require service deployment across multiple clusters or have cross-region disaster recovery needs.

### Applicable Scenarios

- Microservices are distributed across multiple Kubernetes clusters.
- High availability and disaster recovery capabilities across regions are needed.
- Desire to achieve cross-cluster traffic management and service governance.

## Conclusion

When choosing a service mesh deployment model, the specific business needs and application scenarios should be considered. A single-cluster service mesh is suitable for simple application scenarios within a single cluster, while a multi-cluster service mesh offers higher flexibility and reliability for more complex scenarios that require cross-cluster and cross-region capabilities. With an understanding of these two deployment models, you can find detailed operational guides and parameter descriptions in the subsequent chapters to better configure and manage your service mesh.
