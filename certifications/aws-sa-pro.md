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
=======

## VPC

### Security Groups

* No explicit deny
* Allow referencing logical resources, like other security Groups
  * self-referencing can be done
* Attached to ENI (Elastic Network Interface)

### BGP (Border Gateway Protocol)

* AS - Autonomous System - routers controller by one entity, network in BGP
* ASN Range is 0-65535, controlled by IANA
  * private range is 64512-65534
* Operates over tcp port  137
* Peering between ASs is manually configured
* Path-vector protocol is exchanges the best path to destination between peers - ASPATH
* iBGP - internal BGP - routing within an AS
* eBGP - external BGP - routing between ASs
* in path table, i is the origin
* By default, BGP does not take link speed to account, only the path length
  * this can overridden with AS Path Prepending
  * this is done by adding artificial ASs to AS Path

### AWS Global Accelerator

* Anycast IPs allow ip address to be in multiple location at the same time
  * Routing moves traffic to the closest location
* Global Accelerator Edge Location has pair of anycast IPs
  * traffic can be the routed to the closest location
* When traffic is directed to Global Accelerator ip addresses, rest of the transit is using AWS links and networks
* Works with TCP/UDP - difference from CloudFront
* Does not cache anything

### Site-to-site VPN

* Logical connection between a VPC and on-prem network
  * encrypted using IPSec
  * Runs over public internet
* HA - when correctly architected and implemented
* Quick to provision
* Virtual Private Gateway (VGW) is associated to VPC and can be target in route tables
  * has endpoints in each AZ
* Customer Gateway (CGW) logical device to act as VPN connection endpoint 
* VPN connection - linked to one VGW and one CGW
* Static VPC means that VPN connection is statically configured with ip ranges for both sides
* Customer on-prem router is single-point-of-failure - partial high-availability
* There can be two on-prem customer gateways, so the whole architecture is HA
* Dynamic VPN uses Border Gateway Protocol (BGP)
  * Routers exchange routing information
  * can communicate status of links between customer gateways and AWS side
  * if route propagation is enabled, routes are automatically added
* AWS VPN speed limitation is 1,25Gbps and there is encryption overhead
* Latency can be inconsistent, because traffic is routed over public internet
* Cost - AWS hourly cost, GB out cost, data cap (on-premises)
* Can be used as backup for Direct Connect
* Can be used with Direct Connect

### Transit Gateway

* Network transit hub, connecting VPCs to an on-premises network using Direct Connect or Site-to-Site VPN connections
* Network Gateway object - HA and scalable
* Other AWS network resources uses attachments to connect to Transit Gateway 
  * VPC
  * Site-to-site VPN
  * Direct Connect Gateway
* Is capable of transitive routing
* Can be peered with another TGWs - cross-account or cross-region
* TGWs can be shared to other accounts using Resource Access Manager (RAM)
* One DX gateway can be connected to three TGWs
* By default, Transit Gateway has one default route table, which has routes to attached network resources
  * all attachments use this route table for routing decisions
  * all attachments propagate routes to it
  * the two above-mentioned combined means all attachments can route to all attachments
* Routes are not propagated over peering attachments (two peered TGWs) - static routes need to be used
* Use unique ASNs for future route propagation features
* Public DNS to private IP resolution is not supported over peers
* Up to 50 peering attachments per TGW
  * different regions and accounts 
* Data is encrypted
* Attachments can be only associated with one route table
* Route tables can be associated with many attachments
* Attachments can propagate to multiple route tables - even those they are not associated with
* Route table association is used when data is exiting an attachment 
* Route table propagation controls which route tables are populated by routes known by the attachment
* The above-mentioned features can be used to create route isolation

### Advanced VPC Routing

* IPv4 and IPv6 are handed separately within a route table 
* Route table default limit is 50 static routes and 100 dynamic (propagated) routes
* Main route table is implicitly attached to all subnets
* When custom route table is associated to subnet, main route table association is removed
  * However, if custom route table association is removed, main route table applies again 
* It's not possible to have subnet without route table association
* Propagated route tables can be enabled per route table
* More specific route always gets prioritized (for example /28 is selected over /32)
* If prefix length is equal for same route, static route is selected over propagated one
* For the same routes with same prefix lenght learned dynamically, the precedence order is the following:
  * Direct Connect
  * VPN Static
  * VPN BGP
  * AS_PATH (Path between two ASNs, distance between systems)
* Ingress routing
  * Gateway route tables can be used to control actions on inbound traffic for gateway (for example IGW) 
    * for example forward it to security appliance

### IPSEC VPNs

* Group of protocols to setup secure tunnels across insecure networks between two peers (local and remote)
* Only "interesting" traffic is sent over the tunnel
* Provides authentication and encryption
* IPSEC has two phases
  * IKE phase 1 (slow and heavy) - Internet Key Exchange
    * authentication using pre-shared key or certificate
    * uses asymmetric encryption to agree on and create a shared symmetric key
    * end result is IKE SA created (phase 1 tunnel)
    * Uses Diffie-Hellman for key exchange
  * IKE phase 2 (Fast & Agile)
    * uses the keys agreed on phase 1
    * agree encryption method, and keys for bulk data transfer
    * end result is IPSEC SA (security association)
* There are two types of IPSEC VPNs
  * policy-based
    * rule sets match traffic per pair of security associations  
    * allows network to have different security settings for different type of traffic
  * route-based 
    * target matching (prefix) matches single pair of security associations 
* Tunnels have two ends, left and right. Customer and AWS, meaning local and remote.
  * IPs of these ends are referred as AWS outside IP and customer outside IP
* Inside the tunnels there are AWS inside IP address and customer inside IP address  
  * Routing and raw data travels through inside of the tunnel - encrypted traffic runs outside of the tunnel across public internet

# Accelerated Site-to-Site VPN

* By default S2S VPN traffic transit is over public network
* When S2S VPN is used in accelerated mode, AWS Global Accelerator network is used
* VPN tunnel IPs are global, and connections are routed to the closest global accelerator edge location
* Public internet is only used for minimal amount of time to route traffic to nearest edge location
* Acceleration can be only enabled when creating a TGW VPN attachment, not using VPNs with VGW
* Fixed accelerator cost and a transfer fee
* New advanced S2S VPNs features are usually only released for TGW VPN, so the default recommendation is to use it
