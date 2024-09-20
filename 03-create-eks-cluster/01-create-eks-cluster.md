# 0️⃣ - ✡️ Summary

1. Install Amazon EKS cluster with command line
2.

# 1️⃣ - Create Amazon EKS Cluster

- ☕️ It will take 10-15 minutes to create Amazon EKS Cluster Control plane. So,
  take a cup of coffee 😆

```
# Create Cluster
eksctl create cluster --name=vietaws6 \
                      --region=ap-southeast-1 \
                      --zones=ap-southeast-1a,ap-southeast-1b \
                      --without-nodegroup \
                      --profile eks

# Get List of clusters
eksctl get cluster --profile eks
```

#### Enable IAM OIDC for EKS Cluster

Purpose: Enable IAM Role for Service Account on EKS cluster

```
# Syntax
eksctl utils associate-iam-oidc-provider \
    --region region-code \
    --cluster <cluter-name> \
    --approve

# Replace with region & cluster name & profile (optional)
eksctl utils associate-iam-oidc-provider \
    --region ap-southeast-1 \
    --cluster vietaws6 \
    --approve \
    --profile eks

# check oidc on IAM - Identity provider or
aws eks describe-cluster --name vietaws5 --profile eks --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5

```

### create cluster with existing vpc

```
eksctl create cluster --name=vietaws \
                      --region=ap-southeast-1 \
                      #--zones=ap-southeast-1a,ap-southeast-1b \
                      --vpc-from-kops-cluster vpc-018e29483ce154a50 \
                      --vpc-public-subnets=subnet-076c44811e399ab76,subnet-0c3e72a0400d8fb7e  \
                      --without-nodegroup --profile eks
```

# 2️⃣ - Create Amazon EC2 key pair for SSH

1. Go to **Amazon EC2**
2. Go to **Key pairs** and generate new key pair. For example: `eks-kp`

This keypair we will use it when creating the EKS NodeGroup. This will help us
to login to the EKS Worker Nodes using Terminal.

# 3️⃣ - Create Node Group

```
# Create Public Node Group
eksctl create nodegroup --cluster=vietaws \
                       --region=ap-southeast-1 \
                       --name=public-ng1 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access \
                       --profile eks

# Create Private node group
eksctl create nodegroup --cluster=vietaws6 \
                        --region=ap-southeast-1 \
                        --name=ng1 \
                        --node-type=t3.medium \
                        --nodes-min=2 \
                        --nodes-max=4 \
                        --node-volume-size=20 \
                        --managed \
                        --asg-access \
                        --external-dns-access \
                        --full-ecr-access \
                        --appmesh-access \
                        --alb-ingress-access \
                        --node-private-networking \
                        --profile eks
# Get NodeGroups in a EKS Cluster
eksctl get nodegroup --cluster=<Cluster-Name>
eksctl get nodegroup --cluster=vietaws5 --profile eks

# Delete Node Group - Replace nodegroup name and cluster name
eksctl delete nodegroup <NodeGroup-Name> --cluster <Cluster-Name>
eksctl delete nodegroup ng1 --cluster vietaws5 --profile eks

# Create cluster & node group

eksctl create cluster --name=full-eks \
 --region=ap-southeast-1 \
 --zones=ap-southeast-1a,ap-southeast-1b \
 --node-type=t3.medium \
 --nodes=2 \
 --nodes-min=2 \
 --nodes-max=4 \
 --node-volume-size=20 \
 --managed \
 --full-ecr-access \
 --appmesh-access \
 --asg-access \
 --external-dns-access --profile eks
```

# 4️⃣ - Delete

### List EKS Clusters

eksctl get clusters --profile eks

### Capture Node Group name

eksctl get nodegroup --cluster=<clusterName>

eksctl get nodegroup --cluster=vietaws

### Delete Node Group

eksctl delete nodegroup --cluster=<clusterName> --name=<nodegroupName>

eksctl delete nodegroup --cluster=vietaws --name=ng-private-1 --profile eks

### Delete Cluster

eksctl delete cluster <clusterName>

eksctl delete cluster vietaws --profile eks
