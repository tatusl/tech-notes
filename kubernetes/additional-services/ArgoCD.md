## Debugging ArgoCD RBAC

The command `argocd admin settings rbac` can be used to test RBAC access. For example:
`
```shell
argocd admin settings rbac can <account_name> update projects 'default' -n argocd
Yes
```

Currently logged in account privileges can be checked with:

```shell
argocd account can-i
```

