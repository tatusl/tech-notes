# Identity and access management

## Azure AD

* authentication system in Azure
* also authentication system for other Microsoft cloud services like Office 365 etc
* subscriptions are created to Azure AD tenant
* there can be multiple subscriptions under single Azure AD tenant
* Azure AD Connect can synchronize users between on-premise AD and Azure AD 

## RBAC

Allows to authorize to user specific actions to specific Azure resources

* Roles
    * Role definition is a collection of permissions
    * Three built-in roles
        * Owner - can perform all actions on all resource types
        * Contributor - similar than Owner, but does not allow managing RBAC itself
        * Reader - can perform all read actions on all resource types
    * Azure also provides resource specific built-in roles
    * Custom roles can be created 
    * Access to resources can be granted by assigning role to security principal, which are
        * User
        * Group
        * Service Principal
    * Assignment structure
        * Security Principal
        * Role
        * Scope
           * Subscription
           * Resource Group
           * Resource
    * When assignment is done on subscription or resource group level, the resources below in this hierarchy inherit this assignment

## Resources

* Azure Essentials: Identity and Access Management - https://www.youtube.com/watch?v=nRk1_koNBB8
* Understand Azure role definitions - https://docs.microsoft.com/en-us/azure/role-based-access-control/role-definitions
