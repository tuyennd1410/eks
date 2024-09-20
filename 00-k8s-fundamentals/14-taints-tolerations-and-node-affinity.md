# 🌈 Combination between Taints, Tolerations & Node Affinity

## Problem 1: Use ONLY Taints & Toleration

- Taints & Tolerations CANNOT limit the Pod with tolerations to Node without
  taints
- Node Affinity CAN help to fix Pod to Node (above situation)

For example:

- We have 05 nodes: red, green, blue, node1, node2
- Node red, green, blue are tainted
- We have 03 pods: pod-red, pod-green, pod-blue with coresponding tolerations
- ⚠️ pod-red may be schedule on node1 or node2

## Problem 2: Use ONLY Node Affinity

- Other Pods can be scheduled on affinity Nodes

For example:

- We have 05 nodes: red, green, blue, node1, node2
- We have 03 pods: pod-red, pod1, pod2 with affinity configured.
- `pod-red` (use nodeAffinity configuration on Pod manifest) will be only
  schedule on `node red`, but `pod1` and `pod2` maybe schedule on node `green`,
  node `blue`, or `node1`, `node2`. They cannot be restricted. To be restricted,
  you have to use `Taints & Tolerations`
