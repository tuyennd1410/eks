# 🌈 Benefits

- Use to manage application configurations

# 💎 Usage

## 1️⃣ Create ConfigMaps

### Imperative Way

```
# Imperative way
kubectl create configmap <cm-name>
  --from-literal=key1=value1
  --from-literal=key2=value2

kubectl create configmap db-cm \
  --from-literal=DB_HOST="10.1.1.2" \
  --from-literal=DB_PORT="5432"

=> configmap/db-cm created

# get all configmaps
kubectl get cm
NAME               DATA   AGE
db-cm              2      13s

# describe cm
kubectl describe cm db-cm

Name:         db-cm
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
DB_HOST:
----
10.1.1.2
DB_PORT:
----
5432

BinaryData
====
```

### Declarative Way

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: orange
  APP_ENV: prod
```

## 2️⃣ Use ConfigMap in Pod `envFrom`

```
apiVersion: v1
kind: Pod
metadata:
  name: envar-cm-demo
  labels:
    purpose: demonstrate-envars
spec:
  containers:
  - name: envar-demo-container
    image: vietaws/eks:v1
    envFrom:
    - configMapRef:
        name: app-config
```

**Verify**

```
# Configmap
kubectl get cm
NAME               DATA   AGE
app-config         2      3s
db-cm              2      8m40s

# Describe cm
kubectl describe cm app-config

# Pod
kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
envar-cm-demo   1/1     Running   0          51s
envar-demo      1/1     Running   0          25m

# Check env
kubectl exec -it envar-cm-demo -- env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=envar-cm-demo
NODE_VERSION=20.12.2
YARN_VERSION=1.22.19
APP_COLOR=orange
APP_ENV=prod
KUBERNETES_PORT_443_TCP=tcp://10.100.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.100.0.1
KUBERNETES_SERVICE_HOST=10.100.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.100.0.1:443
TERM=xterm
HOME=/root
```

✅ You can see the `APP_COLOR` and `APP_ENV` in the env

## 3️⃣ Extract Single Variable

In above example, we will extract all env vars in the `app-config cm`. If we
want to extract less vars. we will use following syntax.

```
apiVersion: v1
kind: Pod
metadata:
  name: envar-cm-demo
  labels:
    purpose: demonstrate-envars
spec:
  containers:
  - name: envar-demo-container
    image: vietaws/eks:v1
    env:
      - name: MY_APP_COLOR
        valueFrom:
          configMapRef:
            name: app-config
            key: APP_COLOR

```
