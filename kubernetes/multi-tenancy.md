# Multi-tenancy

## Why?

* usually more cost-effective
* easier access to shared resources, for example controllers
* reduced management overhead
* no need to wait for cluster and related infrastructure creation for new tenants

## Why not?

* resource starvation
* security aspects
* hard to get right

## Use cases

* These use cases are listed in David Oppenheimer's talk
* Enterprise
  * users all from same organization
  * users are semi-trusted
  * personas
      * cluster admin
      * namespace admin
      * user
  * vanilla container isolation might be sufficient depending on the workload
  * inter-pod communication might be limited
      * to only within namespace
      * to other namespaces depending on the application topology 
          * one application tier per namespace
* Kubernetes as a Service (KaaS) / Platform as a Service (PaaS)
    * untrusted users running untrusted code
    * users can create namespaces and CRUD non-policy objects with their namespace(s)
    * needs stronger control plane, node, and network isolation
    * resource quotas per how much customer pays
* Software as a Service (SaaS): multi-tenant app
    * customer does not access Kubernetes apiserver, but the application
    * in single instance for all customers model, Kubernetes multi-tenant models are not concerned as multi-tenancy is handled at application level
    * in multi-intance model, each customer has their own application instance
        * in this model proxy can interact with Kubernetes apiserver for creating new application instances
        * namespace per application instance
    * code is semi-trusted
        * if plugins are allowed (for example Wordpress) that code is untrusted
    * certain namespace might host shared infrastructure

## Isolation mechanisms
* namespace isolation
    * best practice is to have namespace per tenant. Or several namespaces per tenant
    * label your namespaces so network policies can target them
    * with labels, network policies can target multiple namespaces
    * most of the isolation features expect this
* Pod Security Policies
    * limit pod/container security contexts
    * for example, pod can not be run as root
* Security contexts
* RBAC
* Network Policies
    * limit network access between tenants
    * need to have enforcing network overlay (CNI)
        * Calico
        * Weave
        * etc
* Secrets
    * can be encrypted at rest with `EncryptionConfiguration` resource
        * `apiserver` needs to be configured with `--encryption-provider-config` option
    * secrets are encrypted at write 
* Scheduling
    * Resource quotas
        * limit size of resource requests and limits per namespace
    * Resource requests
    * Resource limits
        * memory: kills
        * cpu: throttling
    * Limit ranges
        * enforce setting resource limits
    * QoS classes
        * Guaranteed
        * Burstable
        * BestEffort
        * derived from request/limit settings
    * Dedicated nodes (sole-tenant nodes)
        * taints and tolerations
    * Affinity / anti-affinity
        * for example, which pods can co-exist in a node
        * -> pod isolation
    * Priority and preemption (alpha) 
        * low and high priority pods
        * if high-priority pod can not be scheduled, low-priority pod will be killed
* OpenPolicyAgent
    * higher-level enforcing of certain policy sets

## Misc
* Stateful workloads are more complex to manage in multi-tenant setups
* Node security must be kept in mind, because the isolation features of Kubernetes won't help if the node is compromised

## Related

* Software architecture
    * multi-tenant
        * each of the customer in the single application instance and single database
        * single-point-of-failure
    * multi-instance
        * each customer has their own application instance and database
        * stability - no single-point-of-failure
        * each of the instances can be scaled seperately
        * data safety in case of for example security breach

## Resources

* Multi-Tenancy in Kubernetes: Best Practices Today, and Future Directions - David Oppenheimer - https://www.youtube.com/watch?v=xygE8DbwJ7c
* Managing a Multi-Tenanted Kubernetes Cluster in Production by Josh Bowen, Apigee - https://www.youtube.com/watch?v=lA1B2b5kU2g
* Getting started with Kubernetes for your SaaS - https://www.freecodecamp.org/news/getting-started-with-kubernetes-for-your-saas-91e91116dd7d/
* A Pathway to Multi-Tenancy in Kubernetes - https://www.youtube.com/watch?v=Qljlvf4BlZ4
* Kubernetes Multi-Tenancy Best Practices - https://platform9.com/blog/kubernetes-multi-tenancy-best-practices/
* Cluster multi-tenancy - https://cloud.google.com/kubernetes-engine/docs/concepts/multitenancy-overview
* Multi-tenant design considerations for Amazon EKS clusters - https://aws.amazon.com/blogs/containers/multi-tenant-design-considerations-for-amazon-eks-clusters/
* AWS re:Invent 2019: Architecting multi-tenant PaaS offerings with Amazon EKS (GPSTEC337) - https://www.youtube.com/watch?v=P29eL_51iYU
