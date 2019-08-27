# Building Go projects

## Dependencies managed with `dep`

In projects where dependencies are managed with `dep`, the local project needs to be located in `$GOPATH`.

Because of this, using container as a build environment is the easiest solution in most cases.

For example to build the `kube-psp-advisor` project:

```
docker run -it --rm -v "$PWD":/go/src/github.com/sysdiglabs/kube-psp-advisor golang:1.11 /bin/bash
```