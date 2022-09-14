# Moving resources between remote states

Sometimes it's useful to be able to move resources between remote states without a need to recreate infrastructure. One use case is moving resources between directories.

`terraform state mv` command does not directly support remote states, but `-state` and `-state-out` parameters can be used against local states. This means remote states need to fetched first.

This can be done with for example `aws s3 cp` or `terraform state pull > local.tfstate`.

After both states are local, `state mv` command can be used in the following manner:

`terraform state mv -state=src.tfstate -state-out=dst.tfstate module.foo module.foo`

After this modified local state files need to pushed to remote storage, for example with:

`terraform state push /path/to/local/state`

## References:

* https://github.com/hashicorp/terraform/pull/15652#issuecomment-410754814
