# 🌈 Benefits

- Configure env variable for Pod
- You can have multiple env: dev, prod, staging and each env will have separated
  varialbes.
- You can set `env` for each container in a Pod

For example:

- Database hostname
- Database name

# 💎 Usage

## 1️⃣ Use `env`

Configure a static env variables for a Pod

```
apiVersion: v1
kind: Pod
metadata:
  name: envar-demo
  labels:
    purpose: demonstrate-envars
spec:
  containers:
  - name: envar-demo-container
    image: vietaws/eks:v1
    env:
    - name: DB_HOST
      value: "10.10.1.10"
    - name: DB_PORT
      value: "5432"
```

**Verify**

```
kubectl apply -f envar-demo.yaml
=> pod/envar-demo created

kubectl get pods
NAME         READY   STATUS    RESTARTS   AGE
envar-demo   1/1     Running   0          3s

kubectl exec -it envar-demo -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=envar-demo
NODE_VERSION=20.12.2
YARN_VERSION=1.22.19
DB_PORT=5432
DB_HOST=10.10.1.10
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.100.0.1
KUBERNETES_SERVICE_HOST=10.100.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.100.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.100.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
TERM=xterm
HOME=/root
```

✅ You can see `DB_HOST` and `DB_PORT` in the results

To use in application code. You can call env and depends on your programing
language. For example: with javascript, you can use `process.env.DB_PORT`

## 2️⃣ Challenges

- When you have more and more variables or sensitive information (database
  passworkd, api-key), it is hard to manage in a Pod manifest. In this case, we
  will use `ConfigMap` or `Secret` to manage it.
