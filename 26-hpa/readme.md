# Install metrics api server

```
# Verify if Metrics Server already Installed
kubectl -n kube-system get deployment/metrics-server

# Install Metrics
# release: https://github.com/kubernetes-sigs/metrics-server/releases
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.7.1/components.yaml

# Verify
kubectl get deployment metrics-server -n kube-system
```

# Create deployment & increase load

```
# Create Deployment
kubectl apply -f 01-hpa-demo.yaml

# Template
kubectl autoscale deployment <deployment-name> --cpu-percent=50 --min=1 --max=10

# Replace
kubectl autoscale deployment hpa-demo-deployment --cpu-percent=50 --min=1 --max=10

# Describe HPA
kubectl describe hpa/hpa-demo-deployment

# List HPA
kubectl get horizontalpodautoscaler.autoscaling/hpa-demo-deployment
```

# Generate Load

```
# Generate Load

kubectl run test-load1 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://hpa-demo-service.default.svc.cluster.local/

kubectl run test-load2 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://hpa-demo-service.default.svc.cluster.local/

kubectl run test-load3 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://hpa-demo-service.default.svc.cluster.local/

# List all HPA
kubectl get hpa

# List specific HPA
kubectl get hpa hpa-demo-deployment

# Describe HPA
kubectl describe hpa/hpa-demo-deployment

# List Pods
kubectl get pods
```

# CoolDown

- Default cooldown is 5 minutes.

- It will decrease Pods and reach 1 Pod.

```
# Clean
# Delete HPA
kubectl delete hpa hpa-demo-deployment

# Delete Deployment & Service
kubectl delete -f ./01-hpa-demo.yaml
```
