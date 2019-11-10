# Autoscaling

## In general

In general, autoscaling can be done on pod or node level in Kubernetes. In most cases, autoscaling is done on CPU or memory usage, or scheduling constraints. It can also be done on custom metrics and thresholds.

## Kubernetes requests and limits

Kubernetes requests and limits associate with autoscaling in Kubernetes as they

* requests
  * what the scheduler should carve out
* limits
  * enforced at runtime (cgroup)
  * simplified, they are upper bound for resoure usage, making sure that pod does not consumere more resources than what's specified in limits
  * if request is not defined, value of limit is used in its place

## Kubernetes built-in components

### Horizontal pod autoscaler (HPA)

* HPA is a controller and it will scale pods by adding new replicas
* By default HPA will scale by CPU
* Scaling should be set againt deployment, not replica set
* For example: `kubectl autoscale deployment $DEPLOYMENT --cpu-percent=50 --min=1 --max=10`

### Vertical pod autoscaler (VPA)

* In general, VPA scales resources available to pods vertically, meaning pod will get for example more memory and CPU
* TGIK episode 097 did not touch this extensively

### Cluster autoscaler (CA)

* As CA communicates with the API of a cloud provider, there are certain gotchas with the different cloud providers which should be taken into account when setting up CA
  * For example, currently CA can not handle ASGs which spans multiple AZs. Instead, it expects ASG per AZ
* CA will scale nodes up if there are scheduling constraints due to lack of resources 

## Resources

* TGI Kubernetes 097, https://www.youtube.com/watch?v=NY7pyRNrHzE, released 08th November 2018