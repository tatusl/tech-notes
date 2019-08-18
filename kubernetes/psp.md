# Pod Security Policies

Mostly notes from watching TGI Kubernetes 078: Pod Security Policies

* Pod Security Policies (PSP) are mechanism to prevent security aspects and capabilities of containers.
* It is implemented by as an optional, but recommended, admission controller
* Depending on the platform and Kubernetes distribution, PSP might need to enabled first
* PSPs are cluster-wide resources
* Kubernetes objects need to be granted privileges to **use** PSPs by RBAC
  * For example, create ClusterRole and then RoleBinding to bind it to specific namespaces
* In my opinion, PSPs should be used in every production cluster
* It is recommended to create multiple PSPs for different types of workloads. For example:
  * More privileged PSP for kube-system pods. Check TGIK 078 or Kube docs example for this.
  * Restricted for "normal" workloads
  * Something in between, if needed
* You can use `kubectl describe/get $OBJECT` to check which PSP the pod is using from its annotations
* In order to create a default and restrictive policy which always resolves, use ClusterRoleBinding and bind to rule to group `system:authenticated`
* Security context configures security aspects for the pods
* You can provide exceptions to PSPs on
  * workload level
    * binding the role to specific ServiceAccount
  * namespace level
* Helper and auditing tool: https://github.com/sysdiglabs/kube-psp-advisor
  * Advices the set of PSP against your workload

## References

* Kubernetes docs - https://kubernetes.io/docs/concepts/policy/pod-security-policy/
* TGI Kubernetes 078: Pod Security Policies - youtube.com/watch?v=zErhwjPRKn8
* Configure a Security Context for a Pod or Container - https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
