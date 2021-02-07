# Examples

## Docker

Bypass network, pid, and IPC namespaces. Mount host filesystem to `/host` and
chroot to `host` in container.

```docker
docker run -ti 
    --privileged 
    --net=host --pid=host --ipc=host 
    --volume /:/host 
    busybox 
    chroot /host    
```

## Resources

* The Most Pointless Docker Command Ever - https://zwischenzugs.com/2015/06/24/the-most-pointless-docker-command-ever/
