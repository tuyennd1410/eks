# 🌈 Benefits

- Not dependent on k8s Control Plane.
- K8s Control Plane also have some Static Pods such as: api-server, etcd,
  controller-manager

# 💎 Usage

- By default, kubelet will read the pod manifest from
  `/etc/kubernetes/manifests`
- Kubelet will make sure the pod will be alive.

# ✅ Static Pod vs DaemonSet

| **Static Pod**                         | **DaemonSet**                                     |
| -------------------------------------- | ------------------------------------------------- |
| Created by Kubelet                     | Created by Kube api-server (DaemonSet Controller) |
| Deploy k8s Control Plane as Static Pod | Deploy Monitoring Agent, Logger on Worker Node    |

`Both: Ignore kube-scheduler`
