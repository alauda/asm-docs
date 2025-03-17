---
weight: 25
title: Enable Global Rate Limiting
---

If you need to use the global rate limiting feature, you need to enable service global rate limiting by connecting to Redis.

You can connect to the Redis provided by the platform's Data Services, or you can connect to your own Redis.

## Connecting to the Redis Provided by the Platform

If your enterprise has subscribed to **Data Services** and deployed Redis (Cluster Mode, Sentinel Mode) through Data Services that can be connected to the mesh-managed cluster, you can refer to the following steps and instructions to obtain and configure the **Redis Configuration** for the service mesh cluster dimension.

**Obtain Redis Access Method**

1. Switch to the Data Services platform, and select **Redis** from the left navigation bar.

2. In the left cluster list, select the ***cluster, namespace*** where Redis is deployed.

3. Click on the ***Redis Name***.

    * In the **Details** tab, check **Secret Dictionary** to get the password to access Redis.

    * In the **Access Method** tab, you can view the configuration information required to connect to Redis.

**Redis Configuration Instructions**

Based on the **Deployment Mode** and **Deployment Location** of Redis, refer to the following instructions to configure the **Redis Configuration** for the service mesh cluster dimension.

**Note**:

* **Authentication Method**: If the Redis to be connected has a password set, please select **Basic Auth** and enter the password.

* **Address List**: Enter multiple addresses to enhance the availability of the access address, separated by commas.

* **Connection Method**: **Same Cluster** means that Redis is deployed on the cluster to be connected in the mesh; **Cross-Cluster** means that the cluster deploying Redis is not the same cluster as the one to be connected in the mesh.

| Redis Deployment Mode | Connection Method | Mesh to Redis Configuration |
| ------ | -------- | ------- |
| **Sentinel Mode** | **Same Cluster** | **Deployment Mode**: Cluster-Sentinel Mode.<br>**MasterName**: Must enter `mymaster`.<br>**Address List**: Enter the **Internal Access** area display **Connection Address** (`<internal routing name>.<Redis instance namespace name>:<port>` or `<internal routing IP>:<port>`). |
| **Sentinel Mode** | **Cross-Cluster** | **Deployment Mode**: Cluster-Sentinel Mode.<br>**MasterName**: Must enter `mymaster`.<br>**Address List**: Enter the **External Access** area display **Connection Address** (`<Pod IP>:<port>`). |
| **Cluster Mode** | **Same Cluster** | **Deployment Mode**: Cluster-Cluster Mode.<br>**Address List**: Enter the **Internal Access** area display **Connection Address** (`<shard internal routing name>.<Redis instance namespace name>:<port>`). |
| **Cluster Mode** | **Cross-Cluster** | **Deployment Mode**: Cluster-Cluster Mode.<br>**Address List**: Enter the **External Access - Through Pod's NodePort** area display 1 or more Pods containing Nodeport from the shards (`<Pod IP>:<port>`). |

## Connecting to Non-Platform Redis

Please refer to the following configuration instructions to connect to non-platform Redis.

| Parameter | Description |
| ------ | -------- |
| **Deployment Mode** | **Single Node**: Redis deployed on a single node, which lacks disaster recovery capability and is not recommended for production environments.<br>**Cluster-Sentinel Mode**: Refer to the [official documentation](https://redis.io/docs/manual/sentinel/).<br>**Cluster-Cluster Mode**: Refer to the [official documentation](https://redis.io/docs/manual/scaling/). |
| **Address/Address List** | The access address of Redis, supporting HTTP/HTTPS protocol addresses, in the format: `<protocol (optional)>://<IP/domain>:<port>`.<br>**Tip**: Enter multiple addresses to enhance the availability of the access address, separated by commas. |
| **MasterName** | Required only in Sentinel mode, it is the name of the master node. |
| **Authentication Method** | The authentication method when accessing Redis.<br>- **Basic Auth**: Enter the password to access Redis.<br>- **No Authentication**: No authentication required when accessing. |
