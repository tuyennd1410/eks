# Delete nodegroup

```
# Command
eksctl delete nodegroup --cluster vietaws5 --name ng1 --profile eks

# Verify
eksctl get nodegroup --cluster vietaws --profile eks
```

# Delete Fargate Profile

```
# List fargate profile
eksctl get fargateprofile --cluster vietaws5 --profile eks

# Delete profile
eksctl delete fargateprofile --cluster vietaws5 --name profile1 --profile eks
```

# Delete Cluster

```
# Get clusters
eksctl get clusters --profile eks

# Delete cluster
eksctl delete cluster --name karpenter5 --profile eks
```
