# Intro

- The Kubernetes Cluster Autoscale (CA) automatically adjusts the number of
  nodes in your cluster when pods fail to launch due to lack of resources or
  when nodes in the cluster are underutilized and their pods can be rescheduled
  onto other nodes in the cluster.

- Work based on Node Group ASG

- Verify Amazon EC2 Node Group has Auto scaling policy in IAM Role

# Deploy Cluster Autoscaler

```
# Deploy the Cluster Autoscaler to your cluster
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml

# Add the cluster-autoscaler.kubernetes.io/safe-to-evict annotation to the deployment
kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

# Update Cluster Autoscaler Configure

```
kubectl -n kube-system edit deployment.apps/cluster-autoscaler

# Using vi
:set number # set number for vi
:set nonumber # disable number

:50 # go to line 50



# Edit 1 - Go to line 50 (:50)
# Before Change
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<YOUR CLUSTER NAME>

# After Change
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/vietaws6

# Edit 2 - Add two more setting follow-up the above
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
```

# Verify cluster autoscaler image - most related to your EKS cluster

Eg: Cluster is running at 1.29.x

```
# Check current version of image
kubectl -n kube-system get deployment.apps/cluster-autoscaler -o yaml

=>  image: registry.k8s.io/autoscaling/cluster-autoscaler:v1.26.2
=> Cluster is running at 1.29

Find your version: https://github.com/kubernetes/autoscaler/releases

# Template
# Update Cluster Autoscaler Image Version
kubectl -n kube-system set image deployment.apps/cluster-autoscaler cluster-autoscaler=registry.k8s.io/autoscaling/cluster-autoscaler:v1.XY.Z


# Update Cluster Autoscaler Image Version
kubectl -n kube-system set image deployment.apps/cluster-autoscaler cluster-autoscaler=registry.k8s.io/autoscaling/cluster-autoscaler:v1.29.3

# Verify
kubectl -n kube-system get deployment.apps/cluster-autoscaler -o yaml

# View logs
kubectl -n kube-system logs -f deployment.apps/cluster-autoscaler

```

# Deploy Demo App

```
kubectl apply -f 01-app.yaml
```

# Increase replias to SCALE OUT and monitoring

```
# Our ASG has max 4 nodes

# Terminal - 1: Keep monitoring cluster autoscaler logs
kubectl -n kube-system logs -f deployment.apps/cluster-autoscaler

# Terminal - 2: Scale UP the demo application to 30 pods
kubectl get pods
kubectl get nodes
kubectl scale --replicas=30 deploy demo-deployment
kubectl get pods

# Terminal - 2: Verify nodes
kubectl get nodes -o wide
```

# Decrease replicas to SCALE IN

```
# It takes about 10-20 minutes to scale-in
# Terminal - 1: Keep monitoring cluster autoscaler logs
kubectl -n kube-system logs -f deployment.apps/cluster-autoscaler

# Terminal - 2: Scale down the demo application to 1 pod
kubectl scale --replicas=1 deploy demo-deployment

# Terminal - 2: Verify nodes
kubectl get nodes -o wide
```

# Clean

```
# Delete App
kubectl delete -f 01-app.yaml

# Delete CA
kubectl delete -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```
