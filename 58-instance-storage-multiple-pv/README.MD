# Multiple Persistent Volumes, One for Each NVMe Instance Store

In this example, we’ve requested two `i3.8xlarge` EC2 instances.

Region: `ap-southesat-1` (Singapore)

# Create Instance Storage Node Group

```
# Edit 1
- Open ng.yaml change the CLUSTER_NAME & REGION

# Command
eksctl create nodegroup -f ng.yaml --profile eks

⏰ We may need to wait 5-10 minutes to provision the Amazon EC2

# ✅ Verify 1
eksctl get nodegroup --cluster vietaws5 --profile eks

Note: You can also check on AWS Management Console EKS Compute Tab to see node groups

# ✅ Verify 2 - PV
kubectl get pv

# ✅ Verify 3 - SSM to EC2
df -BG | grep nvme

//Output
dev/xvda1          100G    7G       94G   7% /
/dev/nvme0n1       1742G    1G     1654G   1% /mnt/fast-disks/f13c5e50-bde3-4644-95a4-5b035014a803
/dev/nvme1n1       1742G    1G     1654G   1% /mnt/fast-disks/cfa3163d-0bdb-44ab-a3af-3f37357961c8
/dev/nvme2n1       1742G    1G     1654G   1% /mnt/fast-disks/2ef52ee3-8b52-4b69-af5b-114a0064fe88

# ✅ Verify 4 - ds
kubectl get daemonset --namespace=kube-system
NAME                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR              AGE
local-volume-provisioner   1         1         1       1            1           <none>                     135m

```

# Deploy Sample App

```
# Get nodename
kubectl get nodes

# Edit hostname for app
Edit 02-app.yaml line 18

# Create pvc, app
kubectl apply -f manifests

```

# Verify

```
# verify Pod
kubectl get pods

# verify PVC
kubectl get pvc

NAME                   STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
instance-storage-pvc   Bound    local-pv-95048a66   1741Gi     RWO            fast-disks     <unset>                 9m18s

# Verify PV

NAME                CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                          STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
local-pv-3226a05b   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-3609e38c   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-3e3a7e24   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-8abb7f5f   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-95048a66   1741Gi     RWO            Retain           Bound       default/instance-storage-pvc   fast-disks     <unset>                          49m
local-pv-b75fa7e4   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-dc94e7d    1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m
local-pv-e0efeb6b   1741Gi     RWO            Retain           Available                                  fast-disks     <unset>                          49m

✅ PV ID: local-pv-95048a66 BOUND

# Access Pod
kubectl exec -it app-deploy-xxx -- sh

tail -f /data/logs.txt

df -BG | grep nvme

cd /data

ls -lh

du -sh
```

# Clean

```
# Delete app
kubectl delete -f manifests

# Delete NodeGroup
eksctl delete nodegroup --cluster vietaws6 --name eks-pv-nvme-ng --profile eks

# Verify Nodegroup
eksctl get nodegroup --cluster vietaws5 --profile eks

# Delete PV
kubectl delete pv xxx yyy

# Verify PV
kubectl get pv
```
