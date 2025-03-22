---
title: 访问 Bookinfo
weight: 30
sourceSHA: 35030c1db48c8c7001917497f51470dd346c1e905492cd1ebd0ce09980d04e16
---

本指南将引导您通过 Istio Ingress Gateway 访问 Bookinfo 并验证其功能。本文中使用的 Ingress Gateway 基于经典的 Istio `VirtualService` API。

**开始之前，请确保：**

- \[Bookinfo 示例应用已部署]。
- \[服务网格已创建]。
- 您需要平台管理员权限。请确保您的帐户已分配平台管理员角色。

为了简化设置过程，本教程采用 `NodePort` 方法访问 Ingress Gateway，省去了 `LoadBalancer` 的需求。对 Bookinfo 应用的访问将通过节点 IP 和节点端口进行。

## 步骤 1：部署 Ingress Gateway

1. **创建项目和命名空间**
   导航到 **项目管理** 页面，点击 **创建项目**，命名为 `platform`，并选择 Bookinfo 应用所在的集群。
   在项目详情中，左侧导航面板下点击 **命名空间**，创建命名空间 `platform-gateway`。

2. **部署 Ingress Gateway**
   前往 **平台管理**，导航至 **服务网格** > **网关**，点击 **部署网关**。填写网关参数：

   - 基本信息：命名为 `public-ingressgw`，网关类型为 `Ingress Gateway`，网关类型选择为 `Shared`，其他参数保持默认。
   - 部署配置：选择命名空间 `platform-gateway`，节点标签选择 `ingress:true`，其他参数保持默认。
   - 网络配置：选择 `NodePort`，设置 HTTP 协议主机端口为 `30665`，HTTPS 主机端口为 `30666`。
   - 检查端口可用性：执行以下命令以确保端口未被占用：

     ```bash
     kubectl get svc --all-namespaces -o custom-columns='NAMESPACE:.metadata.namespace,NAME:.metadata.name,TYPE:.spec.type,NODEPORT:.spec.ports[*].nodePort' | grep NodePort
     ```

## 步骤 2：创建网关配置

1. 导航至网关详情页面，切换至 **网关配置** 标签，并点击 **创建网关配置**。
2. 命名为 `public-ingressgw-gateway`，选择 `HTTP` 端口 `80`，将主机设置为 `*`，然后点击创建。

## 步骤 3：创建虚拟服务

1. 切换至 **虚拟服务** 标签，点击 **创建路由配置**。
2. 命名为 `public-ingressgw-vs`，选择命名空间 `platform-gateway`，路由目标选择 `命名空间: demo-dev`， `服务: productpage`， `端口: 9080`。

## 测试 Ingress 流量

1. 获取 `GATEWAY_IP_PORT`：
   - 首先，通过执行以下命令查找 Ingress Gateway 所在节点的 IP 地址：

     ```bash
     kubectl get nodes -o wide
     ```

   - 使用节点 IP 结合端口 `30665` 形成 `GATEWAY_IP_PORT`，例如：`192.168.130.0:30665`。

2. 执行以下命令以验证 Ingress Gateway 是否正常工作：

   ```bash
   export GATEWAY_IP_PORT=<node_IP>:30665
   curl -k -s http://$GATEWAY_IP_PORT/productpage | grep -o "<title>.*</title>"
   ```

3. 预期输出应类似于：

   ```bash
   <title>简单书店应用</title>
   ```
