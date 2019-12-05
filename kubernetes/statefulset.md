# StatefulSet

## Resizing EBS disks of StatefulSet without major downtime

Kubernetes has supported resizing of volumes without recreating PVCs and PVs from version 1.11 with certain disk types. EBS is one of them. However, if VolumeClaimTemplate is used certain tricks needs to be done.

* Find PVCs belonging to StatefulSet, for example `kubectl get pvc --all-namespaces`
* Edit PVC API object on-the-fly, for example: `kubectl edit pvc/$PVC_NAME -n $NAMESPACE`
* Change value of field `spec.resources.requests.storage` and save changes
* Ensure that resize is finished by inspecting PVC with `kubectl describe pvc/$PVC_NAME -n $NAMESPACE`. Events field should state that resize is finished and changes take place after pod restart
* Restart pod in question
* Repeat the steps for all PVCs of the StatefulSet
* Delete StatefulSet without deleting its pods: `kubectl delete sts --cascade=false $STS_NAME -n $NAMESPACE`
* Change disk size in VolumeClaimTemplate and apply changes
* If needed, trigger StatefulSet rollout with `kubectl rollout restart sts $STS_NAME -n rabbitmq`. This will restart the pods one-by-one

In addition, Kubernetes has beta support for resizing an in-use PersistentVolumeClaim from version 1.15: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#resizing-an-in-use-persistentvolumeclaim

References:

* https://github.com/kubernetes/kubernetes/issues/68737#issuecomment-498470138
* https://serverfault.com/a/989665
