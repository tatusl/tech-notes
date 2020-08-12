# Hub-spoke network topology

* The hub-spoke is a network topology in Azure where one virtual network acts as a hub, and other virtual networks as spokes. 
* In usual setting, the virtual network acting as a hub hosts central services and connections, like VPN connection to on-premises network. The spokes are connected to the hub with virtual network peering, mening that spokes can use services and connections provided by the hub. 
* In addition, the hub can control connectivity and security, for example using Azure Firewall
* With default configuration, spokes are isolated from each other

## Resources

* Hub-spoke network topology in Azure - https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
* Tutorial: Create a hub and spoke hybrid network topology in Azure using Terraform https://docs.microsoft.com/en-us/azure/developer/terraform/hub-spoke-introduction
