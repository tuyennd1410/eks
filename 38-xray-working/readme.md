# Create IRSA for X-Ray

Ref: https://github.com/aws-samples/aws-xray-kubernetes/tree/master

```
# Template

eksctl create iamserviceaccount \
 --name service_account_name \
 --namespace service_account_namespace \
 --cluster cluster_name \
 --attach-policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess \
 --approve \
 --override-existing-serviceaccounts

# Replace Name, Namespace, Cluster Info (if any changes)

eksctl create iamserviceaccount \
 --name xray-daemon \
 --namespace default \
 --cluster vietaws1 \
 --attach-policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess \
 --approve \
 --override-existing-serviceaccounts \
 --profile eks

 # Verify
 eksctl get iamserviceaccount --cluster vietaws1 --profile eks

 # List k8s Service Accounts
kubectl get sa

# Describe Service Account (Verify IAM Role annotated)
kubectl describe sa xray-daemon

```

# Deploy xray daemon

```
# Deploy
kubectl apply -f 01-xray-daemon.yaml

# Verify Deployment, Service & Pod
kubectl get deploy,svc,pod

# Verify X-Ray Logs
kubectl logs -f <X-Ray Pod Name>
kubectl logs -f xray-daemon-4r2br

# List & Describe DaemonSet
kubectl get daemonset
kubectl describe daemonset xray-daemon
```
