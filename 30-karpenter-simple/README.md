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
    consolidationPolicy: WhenUnderutilized
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
#  amiSelectorTerms:
#    - id: "${ARM_AMI_ID}"
#    - id: "${AMD_AMI_ID}"
EOF

// Output
nodepool.karpenter.sh/default created
ec2nodeclass.karpenter.k8s.aws/default created

# Get nodepool
kubectl get nodepools

# describe nodepool
kubectl describe nodepools default

# describe ec2nodeclass
kubectl describe ec2nodeclass default
```

# Scale Up deployment

```
# Deploy App
kubectl apply -f manifests/app.yaml

kubectl scale deployment inflate --replicas 5
kubectl logs -f -n kube-system -l app.kubernetes.io/name=karpenter -c controller
```

# Scale Down

```
# Command
kubectl delete deployment inflate

# Verify
kubectl logs -f -n kube-system -l app.kubernetes.io/name=karpenter -c controller
```

# Clean Up

```
# Delete Nodepool
kubectl delete nodepool default

# Delete NodeClass
kubectl delete ec2nodeclass default

# Verify Nodepool
kubectl get nodepool

# Verify Ec2NodeClass
kubectl get ec2nodeclass
```
