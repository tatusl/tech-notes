# IAM

## Minimum privilege sets

### ECR

#### Push image

```
"ecr:GetAuthorizationToken",
"ecr:PutImage",
"ecr:InitiateLayerUpload",
"ecr:UploadLayerPart",
"ecr:CompleteLayerUpload",
"ecr:BatchCheckLayerAvailability"
```
