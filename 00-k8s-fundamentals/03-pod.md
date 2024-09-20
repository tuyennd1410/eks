# 0️⃣ List all Pods

```
# Command:
kubectl get pods

# Output example:
NAME             READY   STATUS    RESTARTS   AGE
vietaws-lkmgs    1/1     Running   0          32h
vietaws-qskmg    1/1     Running   0          32h
vietaws-wrrvh    1/1     Running   0          32h
```

Options:

- `kubectl get pods -A` - get all pods for all namespaces
- `kubectl get pods -owide` - get all pods for default namespace with more
  detail

```
# kubectl get pods -owide
NAME             READY   STATUS    RESTARTS   AGE   IP               NODE                                                NOMINATED NODE   READINESS GATES
vietaws-lkmgs    1/1     Running   0          32h   192.168.50.79    ip-192-168-35-199.ap-southeast-1.compute.internal   <none>           <none>
vietaws-qskmg    1/1     Running   0          32h   192.168.16.145   ip-192-168-12-246.ap-southeast-1.compute.internal   <none>           <none>
vietaws-wrrvh    1/1     Running   0          32h   192.168.17.248   ip-192-168-12-246.ap-southeast-1.compute.internal   <none>           <none>
```

# 1️⃣ Create a Pod

```
# Command
kubectl run <pod_name> --image <image_name>

# Example:
kubectl run vietpod1 --image vietaws/eks:v1

# Output example: pod/vietpod1 created
```

## ✅ Verify

### 👍 Check pod running

```
# kubectl get pods
NAME             READY   STATUS    RESTARTS   AGE
vietaws-lkmgs    1/1     Running   0          32h
vietaws-qskmg    1/1     Running   0          32h
vietaws-wrrvh    1/1     Running   0          32h
vietpod1         1/1     Running   0          67s
```

### 👍 Describe pod

```
kubectl describe pods vietpod1
```

### 👍 View pod on node

```
kubectl get pods -owide
```

### 👍 Others

- Access pod's shell: `kubectl exec -it podviet1 -- sh`
- Access pod's log: `kubectl logs podviet1 -f`

# 2️⃣ Delete Pod

```
# Command
kubectl delete pod <pod_name>

# Example:
kubeclt delete pod vietpod1
```
