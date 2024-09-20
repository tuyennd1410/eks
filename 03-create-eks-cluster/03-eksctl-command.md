# Check available Amazon EKS version

```
eksctl version -o json | jq -r '.EKSServerSupportedVersions[]'
```

# View available add-on for cluster version

```
eksctl utils describe-addon-versions --kubernetes-version 1.29 | grep AddonName
```

# Check available version of add-on

```
eksctl utils describe-addon-versions --kubernetes-version 1.29 --name vpc-cni | grep AddonVersion
```

# Check add-on is on AWS or Marketplace (not output is AWS)

```
eksctl utils describe-addon-versions --kubernetes-version 1.29 --name name-of-addon | grep ProductUrl
```

Example

```
eksctl utils describe-addon-versions --kubernetes-version 1.29 --name kubecost_kubecost | grep ProductUrl
```
