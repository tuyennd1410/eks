# 🌈 Benefits

- Use to store sensitive information such as: database password, api-key

# 💎 Usage

## 1️⃣ Create secret

### Imperative Way

```
kubectl create secret generic \
  <secret-name> --from-literal=key1=value1 \
  --from-literal=key2=value2

kubectl create secret generic app-secret \
  --from-literal=DB_HOST=psql \
  --from-literal=DB_NAME=students \
  --from-literal=DB_USER=vietaws \
  --from-literal=DB_PORT=5432 \
  --from-literal=DB_PASSWORD=dbadmin \
```

### Declarative Way

```
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_HOST: cHNxbAo=
  DB_USER: dmlldGF3cwo=
  DB_NAME: c3R1ZGVudHMK
  DB_PORT: NTQzMgo=
  DB_PASSWORD: ZGJhZG1pbgo=
  DB_MSG: SSDinaTvuI8gIFUK
```

⚠️ You have to convert plain text data into encoded format with `base64`

```
# encode
echo dbadmin | base64
=> ZGJhZG1pbgo=

# decode
echo ZGJhZG1pbgo= | base64 --decode
=> dbadmin
```

## 2️⃣ Use in Pod

```
# Verify secret
kubectl describe secrets app-secret
Name:         app-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
DB_HOST:      5 bytes
DB_MSG:       12 bytes
DB_NAME:      9 bytes
DB_PASSWORD:  8 bytes
DB_PORT:      5 bytes
DB_USER:      8 bytes
```

Pod Manifest

```
apiVersion: v1
kind: Pod
metadata:
  name: envar-secret-demo
  labels:
    purpose: demonstrate-envars
spec:
  containers:
  - name: demo-container
    image: vietaws/eks:v1
    envFrom:
    - secretRef:
        name: app-secret
```

**Verify**

```
# Pod
kubectl get pods
NAME                READY   STATUS    RESTARTS   AGE
envar-cm-demo       1/1     Running   0          36m
envar-demo          1/1     Running   0          61m
envar-secret-demo   1/1     Running   0          3s

# Pod detail
kubectl exec -it envar-secret-demo -- env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=envar-secret-demo
NODE_VERSION=20.12.2
YARN_VERSION=1.22.19
DB_HOST=psql
DB_MSG=I ❤️  U
DB_NAME=students
DB_PASSWORD=dbadmin
DB_PORT=5432
DB_USER=vietaws
KUBERNETES_PORT=tcp://10.100.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.100.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.100.0.1
KUBERNETES_SERVICE_HOST=10.100.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
TERM=xterm
HOME=/root
```

✅ You can see all variables from `app-secret`. From application code, you don't
have to decode the secret value again.

‼️ Please remember that `secret` is not encrypted. Only encoded. Do not push
your secret on public repo.

‼️ `Secret`is not encrypted in ETCD.

‼️ Anyone can create Pod/Deployment in same namespace can access secret as well.
Configure least-privilege access to Secrets - RBAC

‼️ Consider to use AWS Secret Manager to manage secret outside of k8s cluster.
