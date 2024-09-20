# 🌈 Understanding

- Kubernetes version for example: v1.29.3
  1. Major version:1
  2. Minor version: 29
  3. Patch: 3
- K8s will release new minor version in few months.
- `alpha` version: new features will be disabled by default
- `beta` version: new features will be enabled.
- ETCD and CoreDNS will have different version number with k8s version. The rest
  is same: api-server, kube-scheduler, controller-manager, kube-let, kube-proxy,
  kubectl

#  1️⃣ Upgrade Process

## Version screw

- X: api-server
- X-1: controller-manager, kube-scheduler
- X-2: kubelet, kube-proxy
- kubectl: X+1 => X-1

For example: api-server's version 1.27

=> controller manager, kube-scheduler: 1.27 or 1.26

=> kubelet, kube-proxy: 1.27, 1.26, 1.25

=> kubectl: 1.28, 1.27, 1.26

‼️ Kubernetes will support 3 latest minor versions at the time.

‼️ Kubernetest recommend to upgrade one minor version at the time. Eg: 1.27 to
1.28, and 1.28 to 1.29

## Upgrade Flow

1. Upgrade master node
2. Upgrade worker node
