# 🌈 Benefits

- **Labels**: use to tag an object
- **Selectors**: use to filter objects based on `Labels`

# 💎 Usage

Under `metadata` section:

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: students
    tier: free
    env: demo
    owner: vietaws
  name: po1
spec:
  containers:
  - image: vietaws/eks:v1
    imagePullPolicy: Always
    name: po1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### Selectors

```
# will match all criterias
kubectl get pods --selector 'key1=value1,key2=value2'
kubectl get pods -l 'key1=value1, key2=value2'

kubectl get pods --selector 'app=students,tier=free'
```

### Use Selector in ReplicaSet

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: vietaws1
  labels:
    name: students
    tier: free
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: free # will group all Pod (line 60)
  template:
    metadata:
      labels:
        tier: free # Pod's label will be used in Selector
        name: students
        type: auto
    spec:
      containers:
        - name: vietaws
          image: vietaws/eks:v1
          imagePullPolicy: Always
```
