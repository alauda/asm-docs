---
weight: 15
title: Creating a Service Mesh
---



This document explains how to create a single-cluster service mesh. Before proceeding, please ensure you are familiar with the following topics:

- Mesh Deployment Models: Select the mesh deployment model that suits your needs.
- Mesh Component Descriptions: Understand the roles of mesh components and prepare the necessary CPU and memory resources for the service mesh.

For instructions on creating a multi-cluster service mesh, please refer to the Multi-Cluster Service Mesh documentation.

## Constraints and Limitations

- Only one service mesh is allowed per cluster.
- When the cluster is a global cluster and the platform is in a disaster recovery environment (i.e., the global has a primary cluster and a disaster recovery cluster), the global cluster cannot deploy a service mesh.
- When the cluster is an IPv4/IPv6 dual-stack network, a service mesh cannot be deployed.

## Prerequisites
Download the Alauda Service Mesh Operator installation package corresponding to your platform architecture.

Upload the Alauda Service Mesh Operator installation package using the Upload Packages mechanism.

Ensure that the cluster has deployed the Prometheus plugin or VictoriaMetrics plugin.

**Note:** When VictoriaMetrics is a multi-cluster deployment architecture, `vmstorage` can be in a different cluster from the service mesh.

Ensure there is an available Elasticsearch. The service mesh can interface with the cluster's Elasticsearch logging plugin or your own Elasticsearch.

When the cluster is an **OpenShift** cluster, the following prerequisites must also be met:

- The namespace `istio-system` has been created.
- Add the `istio-system` namespace to the `anyuid` SCC (Security Context Constraints) group. To do this, log in to the OpenShift cluster's bastion host and execute the command:
  ```shell
  oc adm policy add-scc-to-group anyuid system:serviceaccounts:istio-system
  ```

## Steps

1. In the left navigation bar, click **Service Mesh** > **Mesh**.
2. Click **Create Service Mesh**.
3. Select the cluster and Istio version to deploy the service mesh. In the advanced configuration, ensure the mesh architecture is single-cluster and fill in the interface parameters for Elasticsearch and the monitoring system. You can choose the platform's existing system or an external system.
   - If high availability is strictly required, set Pod anti-affinity to mandatory.
   - Component resources can use default values, but as the scale of services in the mesh grows, the components will need to scale up. Configure alert policies for the mesh components in time to be informed when scaling is needed.

For more information, please see [Mesh Parameter Description](#mesh_parm_desc)

**Note**: When the cluster is an **OpenShift** cluster, the mesh will automatically detect and deploy the `istio-cni` component by default.

## Next Steps

- Enable Istio CNI to eliminate the need for privileged init containers in each Pod.
- Enable Global Rate Limiting.
- Use the Istioctl Tool.
- Monitor Mesh Components.

## <span id="mesh_parm_desc">Mesh Parameter Description</span>

### Global Configuration

The global configuration of the mesh will be applied to all clusters where the mesh is deployed.

| Parameter | Description |
| --------- | ----------- |
| **Interface with Elasticsearch** | **Platform**: Interface with the Elasticsearch logging plugin on any cluster of the platform. Select the cluster where the Elasticsearch logging plugin is located.<br>**External**: Interface with an external Elasticsearch logging plugin. Users need to configure the following parameters:<br>**Access Address**: The access address of Elasticsearch, starting with `http://` or `https://`.<br>**Authentication Method**: The authentication method for accessing Elasticsearch.<br>- **Basic Auth**: Enter the username and password for user identity verification.<br>- **No Authentication**: No authentication required for access. |
| **Interface with Monitoring System** | **Platform**: Interface with the Prometheus plugin or VictoriaMetrics plugin in the cluster.<br>**External**: Interface with Prometheus or VictoriaMetrics provided by an external plugin. Users need to configure the following parameters:<br>**Data Query Address**: The data query address of the monitoring component, starting with `http://` or `https://`.<br>**Authentication Method**: The authentication method for accessing the monitoring system.<br>- **Basic Auth**: Enter the username and password for user identity verification.<br>- **No Authentication**: No authentication required for access.<br>**Note**: Due to the inability of the Prometheus plugin to aggregate monitoring data from multiple clusters, the service mesh under this cluster cannot add more clusters to form a multi-cluster service mesh. |

### Cluster Dimension Configuration

The cluster dimension configuration applies only to the selected cluster.

| Parameter | Description |
| --------- | ----------- |
| **Sidecar Configuration** | **Resource Quota**: The default value of the sidecar resource quota at the cluster level. It can be modified based on actual conditions when injecting sidecars for specific services but cannot exceed the maximum limit of the namespace container quota (LimitRange) where the sidecar is located. |
| **Trace Configuration** | **Sampling Rate**: The default sampling rate of the sidecar trace at the cluster level. |
| **Redis Configuration** | Must be configured only when using the **Global Rate Limiting** function in the data plane. For specific configuration methods, please refer to Enable Global Rate Limiting. |
| **HTTP Retry Policy** | **Retry Count**: The default maximum retry count for HTTP at the cluster level.<br>**Note**: The **Retry Count** in the service routing **Timeout and Retry** policy will override this default value. |

### Component Configuration

**Note**: Mesh components are deployed in specific namespaces of the cluster in the form of Deployments. After the mesh is successfully created, you can view the running status of the components in the **Components** tab, or click the **Component Name** to go to the namespace where the component is deployed on the Container Platform and view the detailed information of the running component's Deployment.

| Parameter | Description |
| --------- | ----------- |
| **Pod Anti-Affinity** | Kubernetes will schedule component Pods to nodes that meet the **Pod Anti-Affinity** settings.<br>**Mandatory**: Only one Pod of the same component is allowed to run on the same node.<br>**Preferred**: Multiple Pods of the same component are allowed to run on the same node. Kubernetes will try to schedule the Pods of the component evenly across available nodes according to the scheduling algorithm, but cannot guarantee that there will be component Pods on each node. |
| **Instance Count** | The expected number of component Pods to run, set according to the actual business request volume. |
| **Resource Quota** | The resource (CPU, memory) request value (requests) for each container instance of the component when it is created, which is also the limit value (limits) of the available resources for the container. Set it reasonably according to the actual business volume and the number of instances. |
| **Deployment Node** | Nodes where the component is deployed. When creating a mesh, the Pods of the component can only be scheduled on the selected nodes. |
| **ELB** | The Huawei Cloud Elastic Load Balancer (ELB) used to provide load balancing capability for the Istio Gateway on the CCE cluster. When the selected cluster is a CCE cluster, you need to fill in the **ELB ID** and **ELB Type** to associate the ELB prepared in advance for the cluster. |
| **Total Resources** | The total resource (CPU, memory) quota required to create all container instances of the component under the current configuration. When the component is enabled for auto-scaling, it is counted according to the maximum number of instances.
