# Debugging

## Run ad-hoc container in cluster

In general:

```
kubectl run -it $IMAGE_NAME --image=$IMAGE --rm --restart=Never -- $COMMAND   
```

For example:

```
kubectl run -i --tty curl --image=tutum/curl --restart=Never --rm -- sh
```
