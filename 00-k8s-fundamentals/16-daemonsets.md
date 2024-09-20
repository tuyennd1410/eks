# 🌈 Benefits

- Run one copy of Pod in each Worker Node.
- When we have new Node in cluster, DaemonSet will place the Pod to Node.
- Use Case: Monitoring Solution, Logs Viewer, kube-proxy, networking solution
- Manifest similar structure with Deployment (remove replicas), but
  Kind:DaemonSet

# 💎 Usage

```
# get all ds for all namespaces
kubectl get ds -A
```

DaemonSet manifest

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  creationTimestamp: null
  labels:
    app: ds1
  name: ds1
spec:
  selector:
    matchLabels:
      app: ds1
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ds1
    spec:
      containers:
        - image: public.ecr.aws/sumologic/kubernetes-fluentd:latest-alpine
          name: eks
          resources: {}
status: {}
```
