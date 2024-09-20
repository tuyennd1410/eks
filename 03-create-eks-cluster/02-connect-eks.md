# 🌻 There are 2 ways you can get the kubeconfig.

## Method 1

Command

```
aws eks update-kubeconfig --alias <alias> --name <clustername> --region <region>
```

Example

```
aws eks update-kubeconfig --alias vietaws5 --name vietaws5 --profile eks
```

Output

```
Added new context arn:aws:eks:ap-southeast-1:825772266789:cluster/cdk to /Users/vietaws/.kube/config
```

‼️ if you provision cluster by AWS CDK. AWS CDK will use an IAM Role (Execution
Role in CDK Tool Cloudformation Stack) to create EKS cluster (master role).

in this case, you have to assume to that role before issuing the command to add
context above.

aws sts assume-role role-arn --assume-name name-of-anything

Copy the access key, secret key, and session token into ~/.aws/credentials file

Run: aws eks update-kubeconfig --name cdk --region ap-southeast-1 --profile ieks

✅ Verify

```
kubectl config get-contexts
```

## Method 2

```

eksctl utils write-kubeconfig --cluster=<clustername>

```

```

```
