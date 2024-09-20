# Install external-dns for Route 53 on EKS

```
# Check installed addons
aws eks list-addons --cluster-name  vietaws6 --profile eks
```

## 1️⃣ Create Route53 Policy on AWS IAM

Policy Name: `AllowExternalDNSUpdates`

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "route53:ChangeResourceRecordSets"
      ],
      "Resource": [
        "arn:aws:route53:::hostedzone/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "route53:ListHostedZones",
        "route53:ListResourceRecordSets"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```

## 2️⃣ - Create IRSA

```
# Template
eksctl create iamserviceaccount \
    --name service_account_name \
    --namespace service_account_namespace \
    --cluster cluster_name \
    --attach-policy-arn IAM_policy_ARN \
    --approve \
    --override-existing-serviceaccounts

# Replaced name, namespace, cluster, IAM Policy arn
eksctl create iamserviceaccount \
    --name external-dns \
    --namespace default \
    --cluster vietaws6 \
    --attach-policy-arn arn:aws:iam::825770460273:policy/AllowExternalDNSUpdates \
    --approve \
    --override-existing-serviceaccounts \
    --profile eks

```

## 3️⃣ - Verify

```
# List Service Account
kubectl get sa external-dns

# Describe Service Account
kubectl describe sa external-dns
Observation:
1. Verify the Annotations and you should see the IAM Role is present on the Service Account

# List IAM Service Accounts using eksctl
eksctl get iamserviceaccount --cluster vietaws6 --profile eks
```

## 4️⃣ - Update External-DNS Pod manifest

Template:
https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/aws.md

Edit:

- #1: Role ARN
- #2: latest external-dns image

kubectl apply -f manifests
