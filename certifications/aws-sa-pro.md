# AWS Certified Solutions Architect Professional

## IAM

### Security Token Service (STS)

* If permission policy of a role is changed, the change is immediately reflected to the privileges of temporary credentials obtained with AssumeRole
* If temporary credentials are leaked, they can revoked by doing the following
  * Update role permission policy with AWSRevokeOlderSessions inline DENY for any sessions older than now
  * In the IAM role UI, there is a helper for that in "Revoke sessions" tab. This will apply the inline policy to role
  * This will revoke all temporary credentials which were obtained before the current, but legitimate users can get new credentials with AssumeRole call
* Fetching EC2 instance role temporary access keys from metadata endpoint
  * Get name of role: `curl http://169.254.169.254/latest/meta-data/iam/security-credentials`
  * Get the credentials: `curl //169.254.169.254/latest/meta-data/iam/security-credentials/$ROLE_NAME`

### Permission boundaries

* Only identity permissions are affected by permission boundaries
  * permission boundary can be applied to IAM Users or IAM Roles 
* They don't grant access by themselves, but only define maximum permissions identity can receive
* Boundaries can be used to solve delegation problems with IAM permissions

### Policy evaluation logic

* Within same AWS account
  * Explicity Deny
  * Organization Service Control Policies (SCPs)
  * Resource Policies - can allow access and evaluation stops
  * IAM Identity Boundaries
  * Sessions Policies
  * Identity Policies
* Cross-acccount access - different AWS accounts
  * needs explicit allow from source account identity policy
  * allow from destination account to source account

### S3 cross-account access

* If bucket ACLs are used, canonical user IDs need to be used to reference other AWS accounts
