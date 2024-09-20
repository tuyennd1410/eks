# Intro

- Deploying the Local Volume Static Provisioner CSI driver using Amazon EKS
  managed node groups and pre-bootstrap commands to expose the NVMe EC2 instance
  store drives as Kubernetes PV objects. Customers may wish to leverage the
  local NVMe storage volumes to achieve higher performance than what’s possible
  from the general-purpose Amazon EBS boot volume.

- Instance storage volumes are for temporary storage, and the data is lost when
  the Amazon Elastic Compute Cloud (Amazon EC2) instance is stopped or
  terminated.

- To persist data stored in instance store volumes across the lifecycle of an
  instance, you need to handle replication at the application layer.

- Non-Namespace resource: pv, clusterroles, clusterrolebindings, storageclasses
  (sc)

📗 Full article:
https://aws.amazon.com/blogs/containers/eks-persistent-volumes-for-instance-store/

# Install Local Storage Static Provider

Purposes:
`detection and creation of PVs for local disks mounted in a predefined file system path`

## ✅ Step 1 - Create SA & Permission on K8s

```
# Command
kubectl apply -f manifests/01-sa.yaml

# Verify
kubectl get sa -n kube-system | grep local
// Output
local-volume-provisioner               0         99s

kubectl get clusterroles | grep local

kubectl get clusterrolebindings | grep local
```

## ✅ Step 2 - CSI Driver ConfigMap

Directory: `/mnt/fast-disk`

```
ConfigMap where to look for mounted EC2 NVMe instance store volumes and how to expose them as PVs. The below ConfigMap specifies that Local Volume Static Provisioner look for mounted NVMe instance store volumes in the /mnt/fast-disk directory.

# Command
kubectl apply -f manifests/02-cm.yaml

# Verify
kubectl get sc
// Output: fast-disks

kubectl get cm -n kube-system | grep local
// Output: local-volume-provisioner-config

kubectl describe cm local-volume-provisioner-config -n kube-system
```

## ✅ Step 3 - Install daemonset (ds)

Target node label: `fast-disk-node` Values: `pv-raid` or `pv-nvme`

```
The DaemonSet in the following manifest specifies a nodeAffinity selector to only schedule the DaemonSet on an Amazon EKS node with a label of fast-disk-node and corresponding value of either pv-raid or pv-nvme.

# Command
kubectl apply -f manifests/03-ds.yaml
// Output: daemonset.apps/local-volume-provisioner created

# Verify
kubectl get ds -n kube-system | grep local

🚨 as of now, you will see there is no running ds pod. because we had configured to limit ds to run on specified nodes with labels fast-disks ONLY.

# Verify DS
kubectl -n kube-system describe ds local-volume-provisioner
// Output: Pods Status:  0 Running / 0 Waiting / 0 Succeeded / 0 Failed
```
