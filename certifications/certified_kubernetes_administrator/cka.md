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

## Pod and node networking

### Pod and node networking

* Linux networking namespaces are used for pod to network connectivity.
* Pod has a ip address and virtual network interface pair. Another one is given to the pod (eth0) and another one is on node.
* Pause container is used to hold a network namespace for a pod
* Pod to pod node communication within node is done with Linux ethernet bridge. Bridge initiates communication with ARP request

### Node to node - CNI
* Pod to pod communication between different nodes is done with a Container Network Interface (CNI)
  * simplified, the source ip address is switched from pods ip to nodes ip. Otherwise, the router would drop the packet
  * this could be also done with Layer 3 routing, but it would be complicated to manage
* CNI is a network overlay, creating tunnel between nodes. This is done by encapsulating packets
* CNI is not built-in to Kubernetes, but can be installed as add-on
* Following are popular CNI plugins
  * calicoho
  * flannel
  * WeaveNet
  * Romana
* Kubelet needs to know that CNI plugin will be used
  * In `kubeadm` this done with `--pod-network-cidr` parameter
  
 ### Networking from internet
  
 #### Services
* Service provides one virtual interface, distributing traffic to pods
* kube-proxy controls `iptables` for traffic routing
* Endpoint object is created with service and it keeps cache of pod ip addresses belonging to that service
* Different service types
  * ClusterIP
  * NodePort
  * LoadBalancer (extension to NodePort)
* LoadBalancer can be set to favor pod on a node with `externalTrafficPolicy=Local` annotation. Otherwise the request could be routed to a pod on a another node, which creates latency

### Ingress
* Ingress is like a LoadBalancer, but it can expose multiple services.
* It exposes http/https routes outside of the cluster to the pods inside
* It also provides Layer 7 routing, based on hostname or path etc.

### DNS
* From version 1.13, coredns is the default DNS service for the Kubernetes
  * service name is still `kube-dns` for backward compatibility
* Pods and services are assigned DNS names
* Headless service is a service without a cluster ip (`clusterIP: None`). It will respond with set of ips, instead of one
* Pods DNS config can be managed with `dnsPolicy` and `dnsConfig`, otherwise the pod will inherit nodes DNS config
  * each ip will point to individual pod of the service

## Scheduling

* Scheduling decisions can be modified with your own scheduling rules
* Scheduler goes through the list of checks when scheduling a pod to a node
* Affinity rules configure for example if the user wants that certain pod goes to a node with certain label
* By default, scheduler tries to spread the pods that belong to same replica set to different nodes
* Multiple schedulers can be run at the same time
  * Pod select their scheduler with annotation
* Taints allow nodes to repel certain pods
* Tolerations are set to pod to allow scheduling to a node with matching taint
* Resource requests (for example CPU and memory) specify the amount of resources a container is quaranteed to get
* Resource limits make sure that a container does not go above certain value
* Limit can never be lower than request
* DaemonSets does not use the scheduler and they run one pod per node
* Scheduling events can be checked (namespace scoped) with a command: `kubectl get events``
* Scheduler logs can be checked like any other logs
