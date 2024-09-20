# Benefits

- Use to remove nodes and combined Pod into a bigger node to cost saving
- Automatic remove / add right worker nodes to workload

- Using `consolidationPolicy`:
  - `WhenUnderutilized`
  - `WhenEmpty`

# Create nodepool & nodeclass

```
# Create nodepool & nodeclass

cat <<EOF | envsubst | kubectl apply -f -
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: default
spec:
  template:
    spec:
      requirements:
        - key: kubernetes.io/arch
          operator: In
          values: ["amd64"]
        - key: kubernetes.io/os
          operator: In
          values: ["linux"]
        - key: karpenter.sh/capacity-type
          operator: In
          values: ["spot"]
        - key: karpenter.k8s.aws/instance-category
          operator: In
          values: ["c", "m", "r"]
        - key: karpenter.k8s.aws/instance-generation
          operator: Gt
          values: ["2"]
      nodeClassRef:
        apiVersion: karpenter.k8s.aws/v1beta1
        kind: EC2NodeClass
        name: default
  limits:
    cpu: 1000
  disruption:
    consolidationPolicy: WhenUnderutilized # WhenEmpty
    expireAfter: 720h # 30 * 24h = 720h
---
apiVersion: karpenter.k8s.aws/v1beta1
kind: EC2NodeClass
metadata:
  name: default
spec:
  amiFamily: AL2 # Amazon Linux 2
  role: "KarpenterNodeRole-${CLUSTER_NAME}" # replace with your cluster name
  subnetSelectorTerms:
    - tags:
        karpenter.sh/discovery: "${CLUSTER_NAME}" # replace with your cluster name
  securityGroupSelectorTerms:
    - tags:
        karpenter.sh/discovery: "${CLUSTER_NAME}" # replace with your cluster name
  amiSelectorTerms:
    - id: "${ARM_AMI_ID}"
    - id: "${AMD_AMI_ID}"
#   - id: "${GPU_AMI_ID}" # <- GPU Optimized AMD AMI
#   - name: "amazon-eks-node-${K8S_VERSION}-*" # <- automatically upgrade when a new AL2 EKS Optimized AMI is released. This is unsafe for production workloads. Validate AMIs in lower environments before deploying them to production.
EOF


# Get nodepool
kubectl get nodepools

# describe
kubectl describe nodepools default
```

# Scale Up deployment

```
# Deploy App
kubectl apply -f manifests/app.yaml

kubectl scale deployment inflate --replicas 8
kubectl logs -f -n kube-system -l app.kubernetes.io/name=karpenter -c controller

# Scale More to 20 replicas
kubectl scale deployment inflate --replicas 20

// Output eks-node-viewer
4 nodes (17650m/27660m) 63.8% cpu ██████████████████████████░░░░░░░░░░░░░░ $0.555/hour | $404.931/month
33 pods (5 pending 28 running 28 bound)

ip-192-168-58-185.ap-southeast-1.compute.internal  cpu ███████████████████████░░░░░░░░░░░░  65% (5 pods)  t3.medium/$0.0528  On-Demand - Ready
ip-192-168-27-29.ap-southeast-1.compute.internal   cpu ███████████████████████░░░░░░░░░░░░  65% (5 pods)  t3.medium/$0.0528  On-Demand - Ready
ip-192-168-102-173.ap-southeast-1.compute.internal cpu █████████████████████████████████░░  95% (18 pods) c5.4xlarge/$0.2643 Spot      - Ready
ip-192-168-68-242.ap-southeast-1.compute.internal  cpu ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░   0% (0 pods)  m4.2xlarge/$0.1848 Spot      - NotReay
```

# Scale Down

```
# Command
kubectl scale deployment inflate --replicas 10

# Verify
kubectl logs -f -n kube-system -l app.kubernetes.io/name=karpenter -c controller
```

# Clean Up

```
# Delete Deployment
kubectl delete deployment inflate

# Delete Nodepool
kubectl delete nodepool default

# Delete NodeClass
kubectl delete ec2nodeclass default

# Verify Nodepool
kubectl get nodepool

# Verify Ec2NodeClass
kubectl get ec2nodeclass
```
