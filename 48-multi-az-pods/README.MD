```
# Edit sc for multi-az

# apply
kubectl apply -f manifest

# check node
kubectl get nodes -owide

# Notes

The Storage Class in this case will only provision one PV and is shared across all pods under the Deployment which uses that PVC.

The accessModes of the PVC does not dictate whether to create one PV for each pod. You can set the accessModes to either ReadWriteOnce/ReadOnlyMany/ReadWriteMany and it will always create 1 PV.

If you want that each Pod will have its own PV, you can not do that under a Deployment

You will need to use StatefulSet using volumeClaimTemplates.

It is Important that the StatefulSet uses volumeClaimTemplates or else, it will still act the same as the Deployment, that is the Storage Class will just provision one PV that is shared across all pods under that StatefulSet.

Link 1: https://stackoverflow.com/questions/41583672/kubernetes-deployments-vs-statefulsets/56865064#56865064
```
