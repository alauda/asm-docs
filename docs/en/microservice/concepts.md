---
weight: 5
title: Concepts
---

## Microservice Concepts

Microservices are a software architecture style that develops and deploys applications as a set of small, independent services. Each service is built around a specific business function and can be independently deployed, scaled, and updated. These services typically communicate through well-defined APIs, usually HTTP RESTful APIs.

## ASM Microservices (ServiceMesh)

The microservices provided by ASM are platform-defined Kubernetes CRD resources (MicroService), which you can manage by creating a MicroService resource.

By adding **ServiceMesh** type microservices, users can integrate any service deployed on the Container Platform (including Deployment and its related resources) as a unified management unit.
This not only simplifies the management process of services but also enables users to fully utilize the powerful service governance and application observability provided by the platform.

After adding microservices, users will have access to a range of advanced features, including but not limited to call chain tracking, global service topology, canary releases, and traffic governance, which effectively enhance the operational efficiency and stability of the system.
These tools allow users to have a deeper understanding and control over service performance, aiding in high-quality service delivery and continuous performance optimization.

**Note**: Some services deployed on the platform using Git or Chart methods can automatically integrate into platform governance. The governance mode on the platform depends on the service configuration, for example, GitOps applications hosted in Git repositories, Java services deployed as OAM applications, Dubbo applications deployed via Chart, etc.
