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
* Cross account S3 access can be done using three methods
  * ACLs - context is the whole bucket and the object
  * Bucket policies - policies can be applied to bucket or objects
  * Assuming role in destination account, which has access to S3 and/or objects

## Resource Access Manager (RAM)

* RAM can be used to share resources between AWS accounts
  * Shared service needs to have RAM support
* As AZ names are rotated between different accounts, AWS introduced AZ ids, which are consistent across accounts
* Resources shared with accounts in same AWS Organization are automatically accepted, if sharing within organization feature is enabled
* This enables for example to create shared services VPC, which can be shared to other accounts
  * Resource-wise, participant accounts only have read-only access
  * each account owns the resources, which it creates to VPC
  * as the resources are in the same VPC, there is network-level connectivity
* Some resources can be shared with any AWS accounts, some can only with AWS acxounts in the same organization
* Name tag is not shared

## Service Quotas

* Service quota request template can be used to request quota changes to all AWS accounts in the same AWS organization
* Cloudwatch Alarms can be created for reaching certain percent of the quota
* Changes to quotas can be done from
  * Console Service Quota service
  * Using support ticket
  * Using AWS API, for example with AWS CLI

## Advanced Identities and Federation

* Use SAML 2.0 (Security Assertion Markup Language) federation if 
  * enterprise identity provider which supports SAML 2.0 is used
  * there is existing identity management team and federation source is not migrated
  *  single source of truth with more than 5000 users
* SAML 2.0 federation uses IAM Roles and AWS Temporary  Credentials (max. 12h duration)
* AWS SSO is preferred for identity federation if there are no exclusions
  * SSO can also manage access for external applications
  * SSO permissions sets bind SSO Users or Groups to certain permissions in one or more AWS accounts
* AWS Cognito
  * provides authentication, authorization, and user management for web/mobile apps
  * two main concepts
  * User Pool - allows to sign-in and get JSON Web Token (JWT)
    * user directory, sign-up, sign-in, and etc.
  * Identity Pool - allows you to offer access to Temporary AWS credentials
    * unauthenticated identities - guest users
    * swap external identities (FB, Google, Apple, SAML 2.0, User Pool) for short term AWS credentials to access AWS resources

## AWS Workspaces 
* AWS Workspaces use Directory Service for authentication and user management
  * simple AD
  * managed full AD
  * external AD with AD connector
* Workspace use VPC networking to access internet or on-prem networks, so there are extra costs
* Can access on-prem resources using VPN or direct connect
* Not HA, because they use only on AZ

## Directory Service

### Microsoft AD

* Native Active Directory
* HA, min. 2 AZs  
* Support one-way and two-way external and forest trusts with on-prem AD
* Supports more than 5000 users
* Automatic patching and maintenance
* Supports AD native schema extensions
* Supports MFA with Radius

### AD connector

* redirects requests to existing directory services
* no directory data stored in AWS 
* requires working network connection to existing AD
* requires two subnets in VPC, different AZs

## VPC

### DHCP in VPC

* By default resources use Amazon provided DNS server in VPC
  * Route53 resolver in VPC
* EC2 instances get private hostnames
* EC2 instances get public hostnames if public ip is configured
* If wanted, user can provide own DNS server
* DHCP is configured with option sets
  * immutable, so can't edited after creation
  * option set can be associated with zero or more VPCs
* Each VPC max one DHCP option set
* When VPC DHCP option set is changed, it requires DHCP renew
* Default gateway in option set can't be changed
  * it's always VPC Router (subnet +1)
* DNS server can be changed, by default it's Route53 Resolver (subnet +2) 

### VPC Router

* HA across all AZs in the region
* Routes traffic between subnets
* Routes from external networks into the VPC and from VPC into external networks
* Has interface in every subnet (subnet+1)
* Controlled usin route tables
* Every VPC in created with default route table
  * it's default for every subnet in VPC
* Custom route table can associated for subnet
* Subnet are associated with one route table
* Most specific route wins in route table
* Local routes always take precedence

### Network Access Control Lists (NACL)

* Every subnet has associated NACL
* NACLs filter traffic crossing the subnet boundary
* Connections within subnet is not impacted by NACLs
* There are inbound and outbound rules
* Rules match dst/src ip range and port. Rules can allow or deny
* Rules are processed in order, lowest number first. Once match occurs, processing stops
* * is an implicit deny, if nothing else matches
* NACLs are stateless, so both inbound and outbound traffic needs to be allowed
* VPC is created with default NACL
* Custom NACLs are created for a specific VPC and are initially associated with no subnets
* At first custom NACLs has only deny all rules
* NACLs can be used to deny specific IPs or ranges, where as security groups can't do that
* Generally security groups are used to allow traffic, NACLs to deny
* Can't reference logical resources and can't be assigned to logical resources
* One NACL can be associated with many subnets
