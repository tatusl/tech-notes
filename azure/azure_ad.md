# Azure AD

## Creating Azure AD users with Terraform

Azure AD users can be managed with Terraform. The following is almost complete example for the key parts:
```hcl
data "azuread_domains" "aad_domains" {}

locals {
  aad_domain  = data.azuread_domains.aad_domains.domains[0].domain_name # assumes that only one domain exists
  users       = [
    "Foo Bar",
    "Bar Baz"
  ]
}

resource "random_password" "rnd_pw" {
  for_each = toset(local.users)

  length  = 16
  special = true
  number  = true
  keepers = {
    name = each.key
  }
}

/*
 * Sets initial password to one generated with Terraform.
 * This password is stored to state. Password change is forced
 * for new users and changes to passwords are ignored by Terraform,
 * so Terraform will not override the new password
 */
resource "azuread_user" "users" {
  for_each = toset(local.users)

  user_principal_name   = "$SOME_REPEATABLE_PATTERN@${local.aad_domain}"
  display_name          = "${each.key}"
  password              = random_password.rnd_pw[each.key].result
  force_password_change = true

  lifecycle {
    ignore_changes = [password]
  }
}
```

Here is a command to fetch users and their initial passwords from Terraform state:

`terraform show -json | jq ' .values.root_module.resources | map(select( .address |contains("azuread_user") )) | map({user_principal_name: .values.user_principal_name, password: .values.password})'
`
