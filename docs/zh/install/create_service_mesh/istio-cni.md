---
weight: 20
title: 启用 Istio CNI
sourceSHA: 6dc1cbadf05066a8a986f44ae309eff3cfa0835b54951c444440f4dd46077e63
---

Istio CNI 节点代理用于为网格中的 Pods 配置流量重定向。它以具有提升权限的方式作为 DaemonSet 在每个节点上运行。Istio CNI 节点代理可用于两种 Istio 数据平面模式。

对于 Sidecar 数据平面模式，Istio CNI 节点代理是可选的。它消除了在网格中的每个 Pod 中运行特权初始化容器的必要性，取而代之的是在每个 Kubernetes 节点上运行一个特权节点代理 Pod。

在 Ambient 数据平面模式中，Istio CNI 节点代理是强制的。

本文档将详细说明如何为 Sidecar 数据平面模式安装和启用 Istio CNI。

**注意：** 如果您是在 **OpenShift** 集群中创建服务网格，Istio CNI 默认启用，因此您无需遵循本文档中的步骤。

## 环境要求

**必须使用 Istio CNI 的环境：**

- 华为云 CCE
- 其他禁止在业务工作负载中使用 NET\_ADMIN 和 NET\_RAW 权限的环境（例如具有严格 PodSecurityPolicy 设置的环境）

**可以使用 Istio CNI 的环境：**

- 理论上支持 CNI 的任何 Kubernetes 集群

**不能使用 Istio CNI 的环境：**

- 禁用 Kubernetes CNI 支持的集群（罕见，通常在轻量级 Kubernetes 开发环境中出现，如 KIND）

## 安装和配置

首先，按常规方式安装服务网格。安装后，暂时不要添加任何服务（即，不要注入 Sidecar）。然后按照以下步骤安装和配置 Istio CNI。

### 安装 Istio CNI 节点代理

修改 ServiceMesh 资源如下：

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  componentConfig:
    # 添加新项：
    # (安装 Istio CNI 插件)
    - name: istioCni  # name 必须为 istioCni
      group: istio
      cni:
        namespace: kube-system  # 通常安装在 kube-system
        # values: # 请参阅 Istio 代码值中的说明。保留以供定制（尤其是对于 Multus CNI，如下面的示例所示）
          # cniBinDir: /opt/cni/bin  # 通常不配置
          # cniConfDir: /etc/cni/net.d
          # chained: false  # 对于 Multus CNI 必须为 false
          # cniConfFileName: "istio-cni.conf"
          # excludeNamespaces:
          #   - istio-system
          #   - kube-system
          # logLevel: info
          # privileged: true
          # provider: multus
```

**注意：** `cni.values` 部分通常无需调整，但如果集群中安装了 Multus CNI，请取消注释并如上述所示进行配置。

**检查 Istio CNI 节点安装是否成功**

```bash
# 如果 Istio CNI 代理安装在其他命名空间，请调整 -nkube-system 部分：
kubectl -nkube-system get po -lk8s-app=istio-cni-node
```

**注意：** 确保所有 CNI 节点代理 Pods 都处于运行状态，然后才能继续下一步。

### 配置 Istiod 以注入 CNI

默认情况下，Istiod 使用 webhook 注入 `istio-init` 容器以实现透明流量拦截。要使用 Istio CNI 代替 `istio-init` 容器，我们需要进一步修改 ServiceMesh 资源如下：

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  istioConfig:
    # 添加 cni 字段
    cni:
      enabled: true  # !重要！启用 CNI 注入。默认为 false
      # chained: true  # 使用 CNI 链式模式，默认为 true。保留以供定制（尤其是对于 Multus CNI，需设置为 false）
```

**注意：** 如果 k8s 集群中已安装 Multus CNI 插件，则 `.spec.istioConfig.cni.chained` 字段必须设置为 false。

应用配置后，Istio Operator 将调整 Istiod 的 webhook，以停止注入 `istio-init` 容器。

## 使用与验证

安装和配置完成后，正常使用服务注入过程，无需任何额外更改。

使用 Istio CNI 注入时：

- `istio-init` 容器将不再存在（透明流量拦截将由 Istio CNI 节点代理处理）。
- 相反，Istio 将注入一个名为 `istio-validation` 的容器，以检查透明流量拦截的 iptables 规则是否成功设置。

## 高级参数

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  componentConfig:
    # (安装 Istio CNI 插件)
    - name: istioCni  # 固定为 istioCni
      group: istio
      cni:
        values:
          repairPods: true
          deletePods: false
          labelPods: false
```

**参数说明：**

- `cni.values.repairPods`：（默认 true）如果 Pod 网络配置异常，尝试重新配置网络。
- `cni.values.deletePods`：（默认 false）如果网络配置异常，自动删除 Pod。
- `cni.values.labelPods`：（默认 false）仅在网络配置异常时标记 Pod（默认标签 `cni.istio.io/uninitialized=true`），允许用户处理。

配置优先级：`repairPods` > `deletePods` > `labelPods`

## 常见问题

Q: 如何确定是否安装了 Multus CNI 插件？

A: 检查集群中的插件列表，以查看平台提供的 Multus CNI 插件是否已安装。如果已安装，请仔细参考安装和配置文档，以确保在 Multus CNI 插件环境中的正常运行。
