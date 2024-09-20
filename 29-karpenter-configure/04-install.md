# 1 - Configure Env

```
# 📗 https://karpenter.sh/docs/getting-started/getting-started-with-karpenter/
# Set Cluster Name

export CLUSTER_NAME="karpenter6"

export K8S_VERSION="1.29"


# Install Env
export KARPENTER_NAMESPACE="kube-system"

export KARPENTER_VERSION="0.37.0"

export AWS_PARTITION="aws" # if you are not using standard partitions, you may need to configure to aws-cn / aws-us-gov

export AWS_DEFAULT_REGION="ap-southeast-1"

export AWS_ACCOUNT_ID="$(aws sts get-caller-identity --query Account --output text)"

export TEMPOUT="$(mktemp)"

export ARM_AMI_ID="$(aws ssm get-parameter --name /aws/service/eks/optimized-ami/${K8S_VERSION}/amazon-linux-2-arm64/recommended/image_id --query Parameter.Value --output text)"

export AMD_AMI_ID="$(aws ssm get-parameter --name /aws/service/eks/optimized-ami/${K8S_VERSION}/amazon-linux-2/recommended/image_id --query Parameter.Value --output text)"

export GPU_AMI_ID="$(aws ssm get-parameter --name /aws/service/eks/optimized-ami/${K8S_VERSION}/amazon-linux-2-gpu/recommended/image_id --query Parameter.Value --output text)"

```

# Verify

```
# Command
echo "${CLUSTER_NAME}" "${KARPENTER_NAMESPACE}" "${KARPENTER_VERSION}" "${K8S_VERSION}" "${AWS_DEFAULT_REGION}" "${AWS_ACCOUNT_ID}" "${TEMPOUT}" "${ARM_AMI_ID}" "${AMD_AMI_ID}" "${GPU_AMI_ID}"

// Output

karpenterX kube-system 0.37.0 1.29 ap-southeast-1 825770460273 /var/folders/qn/23xf1_3d6n53bdjvqtzk6wmm0000gs/T/tmp.w7XaQGRL3B ami-027fd993453df16fd ami-07fde0aa5e0abff1a ami-0800ab92918bbeb54

```

# Create EKS Cluster

```
# Copy and Paste to your Terminal

curl -fsSL https://raw.githubusercontent.com/aws/karpenter-provider-aws/v"${KARPENTER_VERSION}"/website/content/en/preview/getting-started/getting-started-with-karpenter/cloudformation.yaml  > "${TEMPOUT}" \
&& aws cloudformation deploy \
  --stack-name "Karpenter-${CLUSTER_NAME}" \
  --template-file "${TEMPOUT}" \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides "ClusterName=${CLUSTER_NAME}"

eksctl create cluster -f - <<EOF
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: ${CLUSTER_NAME}
  region: ${AWS_DEFAULT_REGION}
  version: "${K8S_VERSION}"
  tags:
    karpenter.sh/discovery: ${CLUSTER_NAME}

iam:
  withOIDC: true
  podIdentityAssociations:
  - namespace: "${KARPENTER_NAMESPACE}"
    serviceAccountName: karpenter
    roleName: ${CLUSTER_NAME}-karpenter
    permissionPolicyARNs:
    - arn:${AWS_PARTITION}:iam::${AWS_ACCOUNT_ID}:policy/KarpenterControllerPolicy-${CLUSTER_NAME}

iamIdentityMappings:
- arn: "arn:${AWS_PARTITION}:iam::${AWS_ACCOUNT_ID}:role/KarpenterNodeRole-${CLUSTER_NAME}"
  username: system:node:{{EC2PrivateDNSName}}
  groups:
  - system:bootstrappers
  - system:nodes
  ## If you intend to run Windows workloads, the kube-proxy group should be specified.
  # For more information, see https://github.com/aws/karpenter/issues/5099.
  # - eks:kube-proxy-windows

managedNodeGroups:
- instanceType: t3.medium
  amiFamily: AmazonLinux2
  name: ${CLUSTER_NAME}-ng
  desiredCapacity: 2
  minSize: 1
  maxSize: 10

addons:
- name: eks-pod-identity-agent
EOF

export CLUSTER_ENDPOINT="$(aws eks describe-cluster --name "${CLUSTER_NAME}" --query "cluster.endpoint" --output text)"
export KARPENTER_IAM_ROLE_ARN="arn:${AWS_PARTITION}:iam::${AWS_ACCOUNT_ID}:role/${CLUSTER_NAME}-karpenter"

echo "${CLUSTER_ENDPOINT} ${KARPENTER_IAM_ROLE_ARN}"

```

# Enable Service Link Role

```
aws iam create-service-linked-role --aws-service-name spot.amazonaws.com || true
# If the role has already been successfully created, you will see:
# An error occurred (InvalidInput) when calling the CreateServiceLinkedRole operation: Service role name AWSServiceRoleForEC2Spot has been taken in this account, please try a different suffix.

```

# Install Karpenter

```

# Logout of helm registry to perform an unauthenticated pull against the public ECR
helm registry logout public.ecr.aws

helm upgrade --install karpenter oci://public.ecr.aws/karpenter/karpenter --version "${KARPENTER_VERSION}" --namespace "${KARPENTER_NAMESPACE}" --create-namespace \
  --set "settings.clusterName=${CLUSTER_NAME}" \
  --set "settings.interruptionQueue=${CLUSTER_NAME}" \
  --set controller.resources.requests.cpu=1 \
  --set controller.resources.requests.memory=1Gi \
  --set controller.resources.limits.cpu=1 \
  --set controller.resources.limits.memory=1Gi \
  --wait

// Output
Error: not logged in
Release "karpenter" does not exist. Installing it now.
Pulled: public.ecr.aws/karpenter/karpenter:0.37.0
Digest: sha256:f1bed2fd948465d505c9ca721190ea8b505386a96be849b5733b6233caa9648d
NAME: karpenter
LAST DEPLOYED: Thu Jun  6 21:56:55 2024
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None

```

# Clean

```

# Delete karpenter

kubectl delete deployment karpenter -n kube-system

# Delete Addon

eksctl delete addon --cluster karpenter6 --name eks-pod-identity-agent

# Delete nodegroup

eksctl delete nodegroup --cluster karpenter6 --name karpenter6-ng

# Delete Cluster

eksctl delete cluster --name karpenter6

```
