# 🌈 Benefits

- Provide isolation group of resources in a k8s cluster.
- Name of resources will be unique in same namespace, but not across namespace.
- Multiple environments for multiple teams, projects.
- Namespace is a solution to divide cluster resources to multiple users by using
  `resource quota` https://kubernetes.io/docs/concepts/policy/resource-quotas/
- There are 03 created namespaces when you provision a cluster

  1. default
  2. kube-system
  3. kube-public

- You can specify namespace in a manifest

# 💎 Usage

## 1️⃣ Basic operations

```
# check namespaces
kubectl get namespaces
kubectl get ns

NAME              STATUS   AGE
default           Active   12d
kube-node-lease   Active   12d
kube-public       Active   12d
kube-system       Active   12d

# create a namespace
kubectl create ns dev

# deploy a pod in a namespace
kubectl run vietaws/eks:v1 app1 -n dev

# check pod
kubectl get pods -n dev

NAME   READY   STATUS    RESTARTS   AGE
app1   1/1     Running   0          4s

# delete a name space will delete all resources
kubectl delete ns dev
=> namespace "dev" deleted

# set default namespace
kubectl config set-context $(kubectl config current-context) -n dev
kubectl config set-context --current --namespace dev

# Check resources belong to a namespace
kubectl api-resources --namespaced=true

# Check resources not belong to namespace
kubectl api-resources --namespaced=true


```

## 2️⃣ Access cross namespace

### Service url syntax

Syntax: `ServiceName`.`Namespace`.`Service`.`Domain`

For example: db-service.dev.svc.cluster.local

### Connect

You can connect Application Pod from `test` ns to `dev` ns

For example: mysql.connect("db-service.`dev`.svc.cluster.local")

We will learn to block access across namespace in the future lesson.
