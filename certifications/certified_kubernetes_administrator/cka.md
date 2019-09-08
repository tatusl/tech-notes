# Certified Kubernetes Administrator

Random notes mostly from watching Cloud Native Certified Kubernetes Administrator (CKA) material from LinuxAcademy

## Some commands

Check master component statuses:

`kubectl get componentstatus`

Check available api-resources in the cluster:

`kubectl api-resources`

## Cluster bootstrap and creation

### Custom cluster (manual install)

* The recommended way is to run components as pods except kubelet which needs to be run on the node itself. That's because kubelet is responsible for runnning everything else as pods.

* However, the components can be run on the node itself as binaries.

### Creating HA Kubernetes cluster

**Creating cluster with HA control plane protects from kube-system components failure.**

* Each component of the master node can be replicated
* However, some components may need to stay in standby state, in order to eliminate conflicts between replicated components
* Only one scheduler and controller manager can be active at a time, others need to be in standby
  * Standby mode is achieved by leader-election mechanism
    * This is done with `endpoint` resource, which annotation tells the current leader. Once becoming the leader, it must update the annotations. This happens by default every two seconds
* Replicating `etcd`
  * Replicated etcd can be in two topologies
    * stacked topology - each control plane node creates its own local etcd and only communicated with it
    * external topology - etcd is external to the Kubernetes cluster
  * etcd uses Raft consensus algorithm which requires majority. In order to have majority, there needs to be odd number of etcd instances
  
## Backing up a cluster

* Mostly it comes down to backing up the etcd
* Grab and install the etcd-client (https://github.com/etcd-io/etcd/releases)
* Create snapshot with: `ETCDCTL_API=3 etcdctl snapshot save snapshot.db --cacert /etc/kubernetes/pki/etcd/server.crt --cert /etc/kubernetes/pki/etcd/ca.cert --key /etc/kubernetes/pki/etcd/ca.key`
* Also backup certificate and keys from `/etc/kubernetes/pki/etcd/`, they are needed in restore
* Recovery examples https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/recovery.md
