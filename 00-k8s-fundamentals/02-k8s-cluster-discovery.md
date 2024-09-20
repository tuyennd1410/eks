# ✡️ Common commands for k8s cluster

# 1️⃣ Check k8s version

Command:

```
kubectl version
```

Output Example:

```
Client Version: v1.29.4
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.3-eks-adc7111
```

# 2️⃣ Check Node Managed:

Command:

```
kubectl get nodes
```

Output Example:

```
NAME                                                STATUS   ROLES    AGE    VERSION
ip-192-168-12-246.ap-southeast-1.compute.internal   Ready    <none>   3d6h   v1.29.0-eks-5e0fdde
ip-192-168-35-199.ap-southeast-1.compute.internal   Ready    <none>   3d6h   v1.29.0-eks-5e0fdde

```

# 3️⃣ Check running pods

Command:

```
kubectl get pods
```

Output:

```
NAME             READY   STATUS    RESTARTS   AGE
vietaws-lkmgs    1/1     Running   0          30h
vietaws-qskmg    1/1     Running   0          30h
vietaws-wrrvh    1/1     Running   0          30h
vietaws2-2cx8b   1/1     Running   0          30h
vietaws2-7vxs2   1/1     Running   0          30h
vietaws2-hwrsz   1/1     Running   0          30h
```

Other commands:

- `kubectl get pods -owide`
- `kubectl get pods -A`

# 4️⃣ Get all namespaces

Command:

```
kubectl get ns
```

Output example:

```
NAME              STATUS   AGE
default           Active   3d7h
kube-node-lease   Active   3d7h
kube-public       Active   3d7h
kube-system       Active   3d7h
```

#
