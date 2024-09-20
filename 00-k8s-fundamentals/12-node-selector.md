# 🌈 Benefits

- Will map Pod to specified Node

# 💎 Usage

- Label the node
- Use `nodeSelector` attribute under Pod's `spec` to specify Pod to Node.

```
# Check node's labels
kubectl get nodes --show-labels

# label a node
kubectl labels nodes node-name key=value
kubectl labels nodes node1 size=Large

# assign Pod to Node
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: demo
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    size: Large
```

# ⛔️ Limitations

- There is no `OR` condition. For example: Place a Pod to Node'size Large or
  Medium
- There is no `NOT` condition. For example: Place a Pod to Node's size not Small

✅ We will use `Node Affinity` to have more flexible.
