---
weight: 30
title: Mesh Component Monitoring
---

The platform provides monitoring charts for the components of the mesh, making it convenient for you to understand the overall operational status of the mesh.

**Steps**

1. In the left navigation bar, click **Service Mesh** > **Mesh**.

2. Click the ***Service Mesh Name*** for which you want to view the monitoring.

3. Under the **Monitoring** tab, you can view the relevant monitoring data of the mesh.

**Monitoring Metrics Explanation**

| Monitoring Metric | Description |
| --- | ---- |
| **Component Memory Usage Rate** | The memory usage rate of the Service Mesh components deployed in the cluster.<br>**Usage Rate = Component Memory Usage / Component Memory Quota** |
| **Component CPU Usage Rate** | The CPU usage rate of the Service Mesh components deployed in the cluster.<br>**Usage Rate = Component CPU Usage / Component CPU Quota** |
| **Connection Success Rate** | The success rate of connections established between the Service Mesh components and monitoring components, Redis, and Elasticsearch in the cluster.<br>**Connection Success Rate = Number of Successful Connections / Total Number of Connection Attempts** |
