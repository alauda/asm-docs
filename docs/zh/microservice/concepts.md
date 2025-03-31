---
weight: 5
title: 概念
sourceSHA: 7bc1b51db38bcce972a5e7ac6dafa0cd2d3cda7ba400cf98fe0e815b435d06af
---

## 微服务概念

微服务是一种软件架构风格，将应用程序开发和部署为一组小型、独立的服务。每个服务围绕特定的业务功能构建，并可以独立部署、扩展和更新。这些服务通常通过明确定义的 API 进行通信，通常是 HTTP RESTful API。

## ASM 微服务（ServiceMesh）

ASM 提供的微服务是平台定义的 Kubernetes CRD 资源（MicroService），用户可以通过创建 MicroService 资源来管理它们。

通过添加 **ServiceMesh** 类型的微服务，用户可以将任何部署在容器平台上的服务（包括 Deployment 及其相关资源）集成为一个统一的管理单元。这不仅简化了服务的管理过程，还使用户能够充分利用平台提供的强大服务治理和应用可观测性。

添加微服务后，用户将可以访问一系列高级功能，包括但不限于调用链追踪、全局服务拓扑、金丝雀发布和流量治理，这些功能有效提升了系统的操作效率和稳定性。这些工具使用户能够更深入地理解和控制服务性能，有助于高质量的服务交付和持续的性能优化。

**注意**：某些使用 Git 或 Chart 方法在平台上部署的服务可以自动集成到平台治理中。平台上的治理模式取决于服务配置，例如，托管在 Git 仓库中的 GitOps 应用程序、作为 OAM 应用程序部署的 Java 服务、通过 Chart 部署的 Dubbo 应用程序等。

有关 MicroService 资源的详细说明，请参阅 \[微服务资源描述]\({{\<relref "/mesh-u-servicemesh/add-services/1microservice.md">}})。
