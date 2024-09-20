# 1️⃣ Kubernetes's manifest

Sketelon:

```
apiVersion:
kind:
metadata:

spec:


```

Pod Example:

```
apiVersion: v1
kind: Pod
metadata: #Map
  name: vietpod1
  labels: #Map
	  app: vietpod1
    env: dev
    author: vietaws
    tier: free
spec:
  containers: #List
    - name: vietaws #Map
      image: vietaws/eks:v1
      imagePullPolicy: Always
      ports: #List
      - containerPort: 8080

```

**📗 More detail**: https://kubernetes.io/docs/concepts/workloads/pods/

## 💡 Check all Kind & apiVersion

```
kubectl api-resources
```

Output example:

```
NAME                              SHORTNAMES   APIVERSION                        NAMESPACED   KIND
bindings                                       v1                                true         Binding
componentstatuses                 cs           v1                                false        ComponentStatus
configmaps                        cm           v1                                true         ConfigMap
endpoints                         ep           v1                                true         Endpoints
events                            ev           v1                                true         Event
limitranges                       limits       v1                                true         LimitRange
namespaces                        ns           v1                                false        Namespace
nodes                             no           v1                                false        Node
persistentvolumeclaims            pvc          v1                                true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                false        PersistentVolume
```

## 💡 Finding total Kind

```
kubectl api-resources | wc -l
```

➡️ Return total lines with header (minus 1 to get actual resources)

```
 kubectl api-resources | wc -l

61
```

➡️ There are 60 resource types.
