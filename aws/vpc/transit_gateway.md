# AWS Transit Gateway

> A transit gateway is a network transit hub that you can use to interconnect your virtual private clouds (VPC) and on-premises networks.

https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html

* Transit Gateway (TGW) makes it possible to hub-and-spoke network design with multiple VPCs, VPNs, and on-premise networks. 
* TGW acts as hub and other networks connect it. TGW controls routing between networks
* This makes network management easier when comparing to connecting multiple VPCs with only VPC peering
* TGWs can be peered with other TGWs with Transit Gateway Peering

## Attaching VPC to Transit Gateway

* Share Transit Gateway resource to target account (which has the VPC to be attached) using AWS Resource Manager principal association
* Accept the shared resource from target account
* From target account, attach the VPC to TGW using Transit Gateway VPC attachment
* Accept the attachment from account which has the VPC


## Resources:

* https://aws.amazon.com/transit-gateway/
* https://docs.aws.amazon.com/vpc/latest/tgw
