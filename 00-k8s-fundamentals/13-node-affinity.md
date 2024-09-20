# 🌈 Benefits

- Use to schedule Pod to Node flexible way.

📗
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity

# 💎 Usage

There are two type of affinity:

- `requiredDuringSchedulingIgnoredDuringExecution`: The scheduler can't schedule
  the Pod unless the rule is met

- `preferredDuringSchedulingIgnoredDuringExecution`: The scheduler tries to find
  a node that meets the rule. If a matching node is not available, the scheduler
  still schedules the Pod. It looks like `Optional`

\*_IgnoredDuringExecution_ means that the running Pods on Node will continue
RUNNING.

```
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In # NotIn | Exists | Gt | Lt | DoesNotExist
            values:
            - antarctica-east1
            - antarctica-west1
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: registry.k8s.io/pause:2.0
```

## ⚠️ Notes

```
- If you specify both nodeSelector and nodeAffinity, both must be satisfied for the Pod to be scheduled onto a node.

- If you specify multiple terms in nodeSelectorTerms associated with nodeAffinity types, then the Pod can be scheduled onto a node if one of the specified terms can be satisfied (terms are ORed).

- If you specify multiple expressions in a single matchExpressions field associated with a term in nodeSelectorTerms, then the Pod can be scheduled onto a node only if all the expressions are satisfied (expressions are ANDed).

- weight can be between 1 to 100. You can have multiple weights for Pod. K8s will calculate sum for all possible Node and schedule to Node with highest score.
```
