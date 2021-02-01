# Pod Priority

**Pod Priority** is a mechanism in Kubernetes which can make certain pods more
important than others. Therefore, they are favoured when making scheduling
decisions. For example, lower priority pods can be evicted from node to make
room for higher priority pods.

This can be useful for cluster add-ons like ingress controllers, log shippers, etc.

## References

* Pod Priority and Preemption - https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/#effect-of-pod-priority-on-scheduling-order
* Guaranteed Scheduling For Critical Add-On Pods - https://kubernetes.io/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/
