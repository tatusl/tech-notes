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
