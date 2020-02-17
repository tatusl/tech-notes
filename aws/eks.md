# EKS

## Service account IAM roles

### Obtaining CA thumbprint of OIDC provider

AWS IAM identity provider needs to be configured with CA Thumbprint. More specifically, this is a SHA1 fingerprint (in lowercase and without colons) of the root CA certificate.

Thumbprint can be obtained with openssl or other tools, but there couple of shortcuts:

* Oneliner from GH issue (https://github.com/terraform-providers/terraform-provider-aws/issues/10104):

```
echo | openssl s_client -servername oidc.eks.${REGION}.amazonaws.com -showcerts -connect oidc.eks.${REGION}.amazonaws.com:443 2>&- | tail -r | sed -n '/-----END CERTIFICATE-----/,/-----BEGIN CERTIFICATE-----/p; /-----BEGIN CERTIFICATE-----/q' | tail -r | openssl x509 -fingerprint -noout | sed 's/://g' | awk -F= '{print tolower($2)}'
```

* `kubergrunt` tool (https://github.com/gruntwork-io/kubergrunt):

`kubergrunt eks oidc-thumbprint --issuer-url $ISSUER_URL`
