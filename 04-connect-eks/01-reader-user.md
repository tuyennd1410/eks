✅ We will create `developer` user has only read permission

# 1️⃣ - Create RBAC for developer

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: reader
rules:
- apiGroups: ["*"]
  resources: ["secrets", "deployments", "pods", "services", "configmaps"]
  verbs: ["get", "watch", "list"]
---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: reader
subjects:
- kind: Group
  name: reader
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: reader
  apiGroup: rbac.authorization.k8s.io

```

## Create ClusterRole and Binding

```
kubectl apply -f rbac-developer.yaml
```

# 2️⃣ - Create AWS IAM User `Developer`

IAM Policy

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "eksdeveloper",
			"Effect": "Allow",
			"Action": [
			    "eks:DescribeNodeGroup",
			    "eks:ListNodeGroups",
			    "eks:ListClusters",
			    "eks:AccessKubernetesApi",
			    "ssm:GetParameter",
			    "eks:ListUpdates",
			    "eks:ListFargateProfiles"
			],
			"Resource": ["*"]
		}
	]
}
```

# 3️⃣ - Try to connect EKS

```
aws eks update-kubeconfig --name <cluster-name> --alias <alias> --region <region-code> --profile eksdev

aws eks update-kubeconfig --name vietaws5 --alias eksdev --region ap-southeast-1 --profile eksdev

=> Output
An error occurred (AccessDeniedException) when calling the DescribeCluster operation: User: arn:aws:iam::xxxxxxxxx:user/developer is not authorized to perform: eks:DescribeCluster on resource: arn:aws:eks:ap-southeast-1:xxxxxxxxx:cluster/eks-tf
```

# 4️⃣ - Configure `aws-auth` configmap

```
# you have to connect to eks cluster first by using default user.
# if you deploy with Terraform, it is a default profile to create eks in tf config
# if you deploy with eksctl, it is a default profile to create eks in cli
# if you deploy with AWS CDK, you have to assume master-role or cdk toolkit execution role. This is a most complicated option.

# connect command
aws eks update-kubeconfig --name eks-tf --alias eks-tf --region ap-southeast-a --profile eks

# assume role eks - cdk option
aws sts assume-role --role-arn arn:aws:iam::xxxxxxx:role/eks-master-role --role-session-name vieteks --duration-seconds $((8*60*60)) --profile  eks
=> copy access key, secret key, session token into ieks profile

# cdk
aws eks update-kubeconfig --name eks-tf --alias eks-tf --region ap-southeast-a --profile ieks

# verify
kubectl get nodes
NAME                                             STATUS   ROLES    AGE   VERSION
ip-10-0-83-108.ap-southeast-1.compute.internal   Ready    <none>   31m   v1.29.3-eks-ae9a62a
ip-10-0-95-250.ap-southeast-1.compute.internal   Ready    <none>   31m   v1.29.3-eks-ae9a62a

# list configmaps
kubectl get cm -A | grep aws-auth

# describe aws-auth
kubectl -n kube-system describe cm aws-auth

Name:         aws-auth
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
mapRoles:
----
- groups:
  - system:bootstrappers
  - system:nodes
  rolearn: arn:aws:iam::xxxxxxxxx:role/eks-node-group-nodes-tf
  username: system:node:{{EC2PrivateDNSName}}


BinaryData
====

# Edit aws-auth
kubectl -n kube-system edit cm aws-auth

  mapUsers: |
  - userarn: arn:aws:iam::825770460273:user/developer
    username: developer
    groups:
    - reader
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::825770460273:role/eks-node-group-nodes-tf
      username: system:node:{{EC2PrivateDNSName}}

```

## ⭐️ Verify

```
aws eks update-kubeconfig --name vietaws5 --alias eksdev --region ap-southeast-1 --profile eksdev

=> Updated context ekstfdev in /Users/vietaws/.kube/config

# check auth
kubectl auth can-i get pods
=> yes

kubectl auth can-i create pods
=> no

```
