# Create AWS CodeCommit Repo

```
# Git init and commit
git init
git add .
git commit -m 'first commit'
git remote add main https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/demo

git push --set-upstream main master
```

# Create AWS CodeBuild

- source: aws codecommit
- project: demo
- branch: master

🚨 Have to enable:
`Enable this flag if you want to build Docker images or want your builds to get elevated privileges`

# Add Codebuild role to Amazon EKS Cluster to deploy application

- EksCodeBuildKubectlRole: used to CodeBuild integrating with Amazon EKS

```
# Trust policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::825770460273:root"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

# Permission

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "eks:Describe*",
            "Resource": "*"
        }
    ]
}
```

- codebuild-role: used to CodeBuild running itself

```
# CodeBuild-Role
arn: arn:aws:iam::825770460273:role/service-role/codebuild-role

#Trust Policy
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "codebuild.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

# Permission:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::825770460273:role/EksCodeBuildKubectlRole"
        }
    ]
}

AWS Managed permission: AmazonEC2ContainerRegistryPowerUser, AWSCodeCommitPowerUser
```

```
# kubectl -n kube-system edit cm aws-auth


mapRoles: |
    - groups:
      - system:masters
      rolearn: arn:aws:iam::825770460273:role/EksCodeBuildKubectlRole
      username: build
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::825770460273:role/eksctl-vietaws
```
