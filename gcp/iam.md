# IAM

## Main concepts

### Permissions

* Permissions are fine-grained permission model defined by GCP services
  * structure: <service>.<resource_type>.<verb> 
  * example: storage.buckets.create

### Roles

* Roles are abstractions to group permissions together
* Primitive roles
  * broad access
  * spans services
  * three roles
    * owner
    * editor
    * viewer
  * Not recommended for production usage
* Predefined roles
  * narrower access
  * permissions to a single service
  * for example
    * service admin
    * service viewer 
* Custom roles
  * create from scratch or from predefined roles
  * can be used to combine roles
  * or remove or add permissions to role

### Bindings

* Bind roles to users, groups (can be nested) or service-accounts

### Policies

* Policies connect the resource, roles and members via bindings

### Resource hierarchy

* Groups resources according to organization structure
* Bindings are inherited down and apply to all nodes under the layer they are applied
* Project provides trust boundary and resource isolation
* The higher up, the more powerful (organization level)
* The lowest level is attaching to certain resource

### Service accounts

* Identity of a service
* They are principal (identity) and resource themselves


## Best practices

* Grant roles to groups, not users. Provides scalability for the future
* Grant least privilege
* Avoid powerful operations, like:
  * Set IAM policy
  * Act as a service account
* Retain audit logs
* Forward events for centralized logging (Stackdriver logging event)

## Resources

* Better Practices for Cloud IAM (Cloud Next '18)') - https://www.youtube.com/watch?v=ZMC8Ng3E3LQ
