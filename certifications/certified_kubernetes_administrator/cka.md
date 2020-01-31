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

## Application lifecycle management

### kubectl commands

Kubectl commands for manipulating application lifecycle

* Create a deployment (with a record for rollbacks)
`kubectl create -f deployment.yaml --record`
* Check status of deployment (rollout)
`kubectl rollout status deployments $DEPLOYMENT`
* Deployment creates the underlying replica set
* Scale deployment
`kubectl scale deployment $DEPLOYMENT --replicas=5`
* Expose the deployment, this is same as creating a service
`kubectl expose deployment $DEPLOYMENT --port $PORT --target-port $TARGET_PORT --type $SERVICE_TYPE`
* Perform rolling update by setting a different Docker image
`kubectl set image deployments/$DEPLOYMENT app=$REPO/$IMAGE:tag`
* Undo rollout and rollback
`kubectl rollout undo deployments $DEPLOYMENT`
* Pause an ongoing rollout
`kubectl rollout pause deployment $DEPLOYMENT`
* Resume the paused rollout
`kubectl rollout resume deployment $DEPLOYMENT`

### Readiness probe

* Readiness probe states when the pod is able to respond to requests

### Passing configuration options

* Environment variables
* ConfigMaps and Secrets (for sensitive data)
   * Can be passed via environment varialbes
   * Or mounted as volume
   
## Data management in the cluster
* Data can be persisted using volumes, for example by provisioning disk from the cloud infrastructure and attaching that to the pod

### Persistent Volumes (PV)
* Persistent Volume resousrce provides higher level abstraction
* PV access modes
  * defines if the volume can be written and read by multiple nodes or just single node
  * RWO (ReadWriteOnce) - only one pod can mount the volume for writing and reading
  * ROX (ReadOnlyMany) - multiple nodes can mount the volume for reading
  * RWX (ReadWriteMany) - multiple nodes can mount the volume for reading and writing
  * Only one mode can be used at the same time
  * Mount capability is for node, not the pod
* `persistenVolumeReclaimPolicy` defines what happens to the PV if the claim is released.
  * Retain - keeps the PV
  * Recycle - Volume can be reused by another PVC
  * Delete - PV will be deleted
* Volumes that are already in use by a pod are protected against data loss.
  * Even if PVC is deleted, volume can be accessed by the pod - Storage object in use Protection
  
### Persistent Volume Claims (PVC)
* When referencing to a PV from a pod, PVC needs to be used
* Abstracts away the storage layer details from the developer
* `storage` attribute can't be higher than in PV resource
* PVC defines the used access mode
* PVCs are namespace specific

### Storage class

* Storage class provides an easy way to create PVCs without defining PV
* Major cloud provide their block disk services as storage classes
* Also hostPath can be used to provision worker node disk.

## Security

### Apiserver authentication

* Evaluation is done whether the request is coming from service account or "normal user"
  * Kubernetes do not have User object, but users can authenticate themselves with
    * private key
    * user store
    * file with a list of user names and passwords
* ServiceAccounts (SA) are for "machine access", if the software needs access to Kubernetes API
  * token (specified as Kubernetes secret) is used for authentication
  * SA can be added to pod by specifying it in the manifest.
  * Default service account is used if SA is not specified
* `kubectl config view` lists address of the cluster in use and authentication mechanism
* It is a good idea to add separate SA for separate pods or replicated pods

### Authorization (RBAC)

* Authentication = who can access
* Authorization = what given entity can do
* In Kubernetes authorization is done by RBAC
* Roles and ClusterRoles
  * what can be done for which resource
* RoleBindings and ClustrerRoleBindings
  * who can do it
* Role and RoleBinding is namespace level
* ClusterRole and ClusterRoleBinding is cluster-level
* Bindings can be done against user, service-accounts or groups

### Network policies

* Govern how pod communicate with each other
* Ingress and egress rules
* CNI needs to support Network polices. For example
  * Canal
  * Calico
* PodSelectors and NamespaceSelectors can be used to select desired pods for which the policies apply

### TLS certificates

* CA is used to generate certificates
* Certificates are used to authenticate to apiserver
* To create new certificates, first a CSR (certificate signing request) needs to be generated using chosen tool
  * like `cfssl` and `cfssljson`
* After that, `CertificateSigningRequest` object needs to be create to Kubernetes API
* CSR needs to be approved: `kubectl certificate approve $CSR_NAME`

### Secure images

* Images come from container registry, by default Docker Hub
* ImagePullPolicy should be `Always` as other users have access to local Docker images even if they don't have ImagePullSecrets
* ImagePullSecrets can be used to define credentials to private Docker registries

### Security Contexts

* Can be used to limit pod or container access to certain objects.
* For example limit container running as root: `runAsUser: $UID` or `runAsNonRoot: true`
* If container needs kernel capabilities of a need, it can be run in privileged mode: `privileged: true`
* Kernel features can be lock-down from a pod by setting capabilities in manifest:
```
capabilities:
  add:
    - SYS_TIME
```
* Capabilities can dropped with `drop` keyword
* Container filesystem can be set to read-only with `readOnlyRootFilesystem`
* If security context is specified in pod level, all containers of the pod inherit it
* However, it can be also specified for certain container

### Secrets

* Can be passed to a pod or container as environment variables or as a file via volumes
* Applications can dump their environment variables for example in crash reports, so passing them as volumes are preferred
* Secrets shared as volumes used `tmpfs`, so they are written to memory, not to disk
