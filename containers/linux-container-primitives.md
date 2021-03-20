# Linux Container Primitives

> Containers are an abstraction over several different Linux technologies

## Linux Kernel

### Namespaces

* "What you can see"
* isolation mechanism for processes
* changes to resources within namespace can be invisible outside the namespace
* examples of available namespaces
  * network
  * filesystem (mount)
  * processes
* namespaces can be shared with process
* OR process can have its own namespaces, like a container
* commonly used namespaces in containers
  * network
    * `veth` devices (or pairs) can connect different namespaces
    * Docker uses separate network namespace per container
    * by default Docker connects container network with `veth` pair to Linux bridge to allow outbound connectivity 
    * in Kubernetes, containers in a pod share the same network namespace
  * mount
    * used to give containers their own filesystem
  * `procfs` virtual filesystem
    * introspection of Linux kernel data
* Linux syscalls are used working with namespaces
  * clone
  * unshare
* namespace can not be empty
* tools to enter namespaces
  * nsenter
  * ip-netns
* Leveraging namespaces with containers
  * `nsenter` or `ip netns` to troubleshoot container networking
  * monitor containers by entering the `pid` namespace
  * access binaries in your containers with the mount namespace
* Further reading: `man 7 {namespaces, pid_namespaces, user_namespaces}`

### Control groups

* "What you can use"
* Cgroups
* Organizes all processes in the system, whether they are in container or not
* Account for resource usage and gather utilization data
* Limit or prioritize resource utilization
* Cgroup system is an abstract framework
    * subsystem are concrete implementations
      * memory
      * cpu time
      * block I/O
      * number of discrete processes (pids)
      * devices
      * etc
    * subsystems are independent
* cgroups can be interacted with throught virtual filesystem
  * typically in `ace}/sys/fs/cgroup`
  * `tasks` file holds all pids in cgroup
* get cgroups for pid: `cat /proc/$PID/cgroups`


### Filesystems

* images are representation of a filesystem
* Docker uses image layers 
* Layers are typically implemented with union filesystems
  * efficient use of storage when there are only minor modifications to image
* Overlay filesystem is built in to Linux
* Docker's default layer storage uses the overlay filesystem
* Leveraging with Docker
  * locate files in your layers
  * examine which layers files contribute to disk usage
  * understand the impact of writable files in your containers

## Container runtimes

* software tool that configures Linux primitives to create and run containers on a host
* Examples include
  * Docker
  * containerd
  * runc
  * CRI-O
  * systemd-nspawn
* `runc` is OCI (Open Containers Initiative) reference implementation which powers Docker, containerd and CRI-O
* OCI runtime spec
  * containers are "bundles"
    * Filesystem
    * JSON document
      * describes underlying technologies which "make the container" like cgroups, namespaces, Linux capabilities, and more 

## Resources

* Linux Container Primitives: cgroups, namespaces, and more! - https://www.youtube.com/watch?v=x1npPrzyKfs
* CGroups documentation - https://www.kernel.org/doc/Documentation/cgroup-v1/
* Overlay filesystem documentation - https://www.kernel.org/doc/Documentation/filesystems/overlayfs.txt
* Golang UK Conf. 2016 - Liz Rice - What is a container, really? Let's write one in Go from scratch - https://www.youtube.com/watch?v=HPuvDm8IC-4
