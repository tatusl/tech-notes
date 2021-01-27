# Azure CDN

## HTTP to HTTPS redirect

CDN endpoint rules engine can be used to redirect HTTP request to HTTPS port.

In Terraform, this can be achieved with the following `delivery_rule` block
for `azure_endpoint_resource`:

```hcl
delivery_rule {
    name  = "EnforceHTTPS"
    order = "1"

    request_scheme_condition {
      operator     = "Equal"
      match_values = ["HTTP"]
    }

    url_redirect_action {
      redirect_type = "Found"
      protocol      = "Https"
    }
}
```

Azure docs shows how to do this from Portal https://docs.microsoft.com/en-us/azure/cdn/cdn-standard-rules-engine#redirect-users-to-https

## Resources

* Migrating a Static Site to Azure with Terraform - https://www.emilygorcenski.com/post/migrating-a-static-site-to-azure-with-terraform/
* Set up the Standard rules engine for Azure CDN - https://docs.microsoft.com/en-us/azure/cdn/cdn-standard-rules-engine#redirect-users-to-https 
