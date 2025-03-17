---
weight: 20
title: Enable Istio CNI
---

The Istio CNI node agent is used to configure traffic redirection for Pods in the mesh. It runs as a DaemonSet on each node with elevated permissions. The Istio CNI node agent can be used in two Istio data plane modes.

For the Sidecar data plane mode, the Istio CNI node agent is optional. It eliminates the need to run privileged init containers in each Pod within the mesh, replacing them with a single privileged node agent Pod on each Kubernetes node.

In the Ambient data plane mode, the Istio CNI node agent is mandatory.

This document will detail how to install and enable Istio CNI for the Sidecar data plane mode.

**Note:** If you are creating a service mesh in an **OpenShift** cluster, Istio CNI is enabled by default, so you do not need to follow the steps in this document.

## Environment Requirements

**Environments that must use Istio CNI:**
- Huawei Cloud CCE
- Other environments that prohibit the use of NET_ADMIN and NET_RAW permissions in business workloads (such as those with strict PodSecurityPolicy settings)

**Environments where Istio CNI can be used:**
- Any Kubernetes cluster with CNI support enabled in theory

**Environments where Istio CNI cannot be used:**
- Clusters with Kubernetes CNI support disabled (rare, typically found in lightweight Kubernetes development environments like KIND)

## Installation and Configuration

First, install the service mesh as usual. After installation, do not add any services (i.e., do not inject the Sidecar) for the time being. Then follow the steps below to install and configure Istio CNI.

### Installing the Istio CNI Node Agent

Modify the ServiceMesh resource as follows:

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  componentConfig:
    # Add a new item:
    # (Install Istio CNI plugin)
    - name: istioCni  # name must be istioCni
      group: istio
      cni:
        namespace: kube-system  # typically installed in kube-system
        # values: # refer to the descriptions in the Istio code values. Reserved for customization (especially for Multus CNI, as shown in the example below)
          # cniBinDir: /opt/cni/bin  # usually not configured
          # cniConfDir: /etc/cni/net.d
          # chained: false  # must be false for Multus CNI
          # cniConfFileName: "istio-cni.conf"
          # excludeNamespaces:
          #   - istio-system
          #   - kube-system
          # logLevel: info
          # privileged: true
          # provider: multus
```

**Note:** The `cni.values` section generally does not need to be adjusted, but if the cluster has Multus CNI installed, uncomment and configure as shown above.

**Check Istio CNI Node Installation Success**

```bash
# Adjust the -nkube-system part if the Istio CNI agent is installed in another namespace:
kubectl -nkube-system get po -lk8s-app=istio-cni-node
```

**Note:** Ensure all CNI node agent Pods are in the Running state before proceeding to the next step.

### Configuring Istiod to Inject CNI

By default, Istiod uses a webhook to inject the `istio-init` container for transparent traffic interception. To use Istio CNI instead of the `istio-init` container, we need to further modify the ServiceMesh resource as follows:

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  istioConfig:
    # Add cni field
    cni:
      enabled: true  # !Important! Enable CNI injection. Default is false
      # chained: true  # Use CNI chained mode, default is true. Reserved for customization (especially for Multus CNI, which needs to be set to false)
```

**Note:** The `.spec.istioConfig.cni.chained` field must be set to false if the k8s cluster has the Multus CNI plugin installed.

After applying the configuration, the Istio Operator will adjust Istiod's webhook to stop injecting the `istio-init` container.

## Usage & Verification

After installation and configuration, use the normal service injection process without any additional changes.

When using Istio CNI injection:
- The `istio-init` container will no longer be present (transparent traffic interception will be handled by the Istio CNI node agent).
- Instead, Istio will inject a container named `istio-validation` that checks if the iptables rules for transparent traffic interception are successfully set.

## Advanced Parameters

```yaml
apiVersion: asm.alauda.io/v1alpha1
kind: ServiceMesh
spec:
  componentConfig:
    # (Install Istio CNI plugin)
    - name: istioCni  # fixed as istioCni
      group: istio
      cni:
        values:
          repairPods: true
          deletePods: false
          labelPods: false
```

**Parameter Descriptions:**

- `cni.values.repairPods`: (default true) Attempts to reconfigure the network if the Pod network configuration is abnormal.
- `cni.values.deletePods`: (default false) Automatically removes the Pod if the network configuration is abnormal.
- `cni.values.labelPods`: (default false) Only labels the Pod (default label `cni.istio.io/uninitialized=true`) if the network configuration is abnormal, allowing the user to handle it.

Configuration Priority: `repairPods` > `deletePods` > `labelPods`

## FAQ

Q: How to determine if the Multus CNI plugin is installed?

A: Check the plugin list in the cluster to see if the platform-provided Multus CNI plugin is installed. If it is, please carefully refer to the installation and configuration documentation to ensure normal operation in the Multus CNI plugin environment.
