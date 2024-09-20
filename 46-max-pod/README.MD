# EKS limits number of pod per instance type

The formula for defining the maximum number of pods per instance is as follows:

`N * (M-1) + 2`

Where:

- N is the rnumber of Elastic Netwok Interfaces (ENI) of the instance type

- M is the number of IP addresses of a single ENI

So, for t3.small, this calculation is `3 * (4-1) + 2 = 11`.

`Increase IP for Node`:
https://docs.aws.amazon.com/eks/latest/userguide/cni-increase-ip-addresses.html

# How to find N and M

Ref:
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI

```
# Template
aws ec2 describe-instance-types --filters "Name=instance-type,Values=t3.*" --query "InstanceTypes[].{Type: InstanceType, MaxENI: NetworkInfo.MaximumNetworkInterfaces, IPv4addr: NetworkInfo.Ipv4AddressesPerInterface}" --output table

# Output

--------------------------------------
|        DescribeInstanceTypes       |
+----------+----------+--------------+
| IPv4addr | MaxENI   |    Type      |
+----------+----------+--------------+
|  15      |  4       |  t3.2xlarge  |
|  15      |  4       |  t3.xlarge   |
|  12      |  3       |  t3.large    |
|  6       |  3       |  t3.medium   |
|  2       |  2       |  t3.micro    |
|  2       |  2       |  t3.nano     |
|  4       |  3       |  t3.small    |
+----------+----------+--------------+
```

# Test max Pod

```
# Check pod per node
# Go to SSM of Worker node - Using AWS Management Console - Connect
crictl ps

kubectl apply -f manifests

kubectl scale deployments.apps app-deployment --replicas=12

# Count number of Pod. For example: 15

# Calculate max Pod per instance. For example: t3.medium has 17 Pods

# Update app.yaml hostname nodeSelector
kubernetes.io/hostname: ip-192-168-124-198.ap-southeast-1.compute.internal

# Update the app.yaml replicas: 3 (over 1 pod)


# Get Pods per Nodename
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name --all-namespaces

# Get Pods per Specifi Noeddename
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name --all-namespaces | grep ip-192-168-124-198.ap-southeast-1.compute.internal

# Count Pod per Nodename
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name --all-namespaces | grep ip-192-168-100-110.ap-southeast-1.compute.internal | wc

# Get Pod's IP per Nodename
kubectl get pod --all-namespaces -o json | jq '.items[] | .spec.nodeName + " " + .status.podIP'

kubectl get pod --all-namespaces -o json | jq '.items[] | .spec.nodeName + " " + .status.podIP' | grep ip-192-168-100-110.ap-southeast-1.compute.internal

kubectl get pod --all-namespaces -o json | jq '.items[] | .spec.nodeName + " " + .status.podIP' | grep ip-192-168-124-198.ap-southeast-1.compute.internal | wc

```
