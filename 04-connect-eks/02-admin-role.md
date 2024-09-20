# 1️⃣ - Create IAM Admin Poliy

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "eks",
			"Effect": "Allow",
			"Action": ["eks:*"],
			"Resource": ["*"]
		},
		{
		  "Sid": "passRole",
			"Effect": "Allow",
			"Action": ["iam:PassRole"],
			"Resource": ["*"],
      "Condition":{
          "StringEquals":{
              "iam:PassedToService": "eks.amazonaws.com"
          }
      }
		}
	]
}
```

# 2️⃣ - Create IAM Role

Trust Policy

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::xxxxxxxx:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {}
        }
    ]
}
```

Verify Role

```
aws iam get-role --role-name eks-admin-role --profile eks
```

# 3️⃣ - Update aws-auth

```
# kubectl -n kube-system edit cm aws-auth

  mapRoles: |
    - rolearn: arn:aws:iam::916495840179:role/eks-admin-role
      username: eks-admin-role
      groups:
      - system:masters
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::xxxxxxx:role/eks-node-group-nodes-tf
      username: system:node:{{EC2PrivateDNSName}}
  mapUsers: |
    - userarn: arn:aws:iam::xxxxxxx:user/developer
      username: developer
      groups:
      - reader
```

# 4️⃣ - Create Admin user & group

Admin permission => `eksadmin` user

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Statement1",
			"Effect": "Allow",
			"Action": ["sts:AssumeRole"],
			"Resource": ["arn:aws:iam::xxxxxx:role/eks-admin-role"]
		}
	]
}
```

# 5️⃣ - Configure admin-user to assume role

```
# vi ~/.aws/config
[profile default]
[profile eks] # profile to create eks
[profile eksadmin] #only has assume role, want to access cluster
[profile eks-manager] #role to be assumed
role-arn=arn:aws:iam::xxxxxxxx:role/eks-admin-role
source-profile=eksadmin

# connect to eks
aws eks update-kubeconfig --name eks-tf --alias eks-admin --region ap-southeast-1 --profile eks-manager

# verify
kubectl config view --minify

kubectl auth can-i "*" "*"
=> yes
```
