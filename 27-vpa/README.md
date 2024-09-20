# Benefits

- Auto scale Pod vertially cpu, ram for cost saving

- Is not out of the box feature like HPA. Have to install to you cluster.

- Components
  1. VPA Admision Hook: Every Pod will go to this hook to check VPA setting
  2. VPC Recommender: Check metrics-server for historical usage of resources,
     generate resource recommendation to scale up or down.
  3. VPC Updater: run every 1 minute. If Pod is not run in recommendation range.
     It evicts the pod, and new Pod will go thru VPC Admision Hook.

# Install VPA

- Require to install metrics-server. Check `HPA` lab

```
# Clone Repo
git clone https://github.com/kubernetes/autoscaler.git

# Navigate to VPA
cd autoscaler/vertical-pod-autoscaler/

# Uninstall VPA (if we are using old one)
./hack/vpa-down.sh

# Install new version of VPA
./hack/vpa-up.sh

# Verify VPA Pods
kubectl get pods -n kube-system
```

# Deploy App Demo

```
# Deploy Application
kubectl apply -f 01-vpa-app.yaml

# List Pods, Deploy & Service
kubectl get pod,svc,deploy

# Describe Pod
kubectl describe pod <pod-name>

# Access Application (If our NodeGroup is in Public Subnet, if not ignore)
kubectl get nodes -o wide
```

# Deploy VPA

```
# Deploy
kubectl apply -f 02-vpa-configure.yaml

# List VPA
kubectl get vpa

# Describe VPA
kubectl describe vpa vpa-vietaws
```

# Generate Load

```
# Generate Load

kubectl run test-load1 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://vpa-demo-service.default.svc.cluster.local/

kubectl run test-load2 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://vpa-demo-service.default.svc.cluster.local/

kubectl run test-load3 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://vpa-demo-service.default.svc.cluster.local/
```

`Note 1` - VPA Updater can re-launch new pod with updated CPU and Memory when
you atleast have 2 pods in a deployment.

`Note 2` - If we have only one pod, unless we manually delete that pod, it will
not launch new pod with VPA recommended CPU and memory considerign the
application availability scenario.

# Clean

kubectl delete -f ./
