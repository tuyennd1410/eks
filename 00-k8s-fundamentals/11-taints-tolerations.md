# 🌈 Summary

- Pod to Node Relationship
- Allow only Pod to Node
- Taints is a configuration on `Node`
- Tolerances is a configuration on `Pod`

📗 https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/

## 💡 Taints

- Will allow to schedule Pod to Node if Pod has tolerances
- K8s will check on Taints labels in a Node.

For example: taints on `Node1` (`app=blue`)

- Node's taint (app=blue) will not allow any Pod's tolerance (app=blue)
- Pod's tolerance (app=blue) can be placed on other Node.

🔑 Taints will not guarantee to restrict Pod's tolerance to `Other Nodes` => We
will use Node Affinity in next lessons to solve this problem.

## 💡 Tolerances

- Will be configured on Pod level and allow Pod to schedule ONLY in Node

# 1️⃣ Usage

### 1 - Check Node Taints

```
kubectl get nodes

kubectl describe node node-name

kubectl describe node node1 | grep -A1 Taints
```

### 2 - Taints Command

```
kubectl taint nodes node-name key=value:taint-effect

taint effect: NoSchedule | PreferNoSchedule | NoExecute

For example:
kubeclt taint nodes node1 app=blue:NoSchedule

kubeclt taint nodes node1 env=demo:NoSchedule
```

### 3 - Tolerance a Pod

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: app1
  name: app1
spec:
  tolerations:
    - key: app
      value: blue
      operator: Equal
      effect: NoSchedule
  containers:
    - image: vietaws/eks:v1
      imagePullPolicy: Always
      name: app1
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

⚠️ The Pod will be in `Pending` state because Node has 02 taints

⚠️ If Node2 has no taints, Pod will be scheduled on Node2

### 4 - Remove a Taint

```
kubectl taint node node-name key:value:effect-

For example:
kubectl taint node node1 env:demo:NoSchedule-

# node/node1 untainted

kubect get pods
```
