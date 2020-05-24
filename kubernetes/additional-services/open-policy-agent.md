# Open Policy Agent (OPA)

> Policy-based control for cloud native environments

* Can be used generally with structured data, not only with Kubernetes
* Uses Rego DSL for its policies
* Rego is for data queries, it's not general purpose language
* Can be used to validate manifests, for example `"Container must provide app label for pod selectors"`
* Output JSON can passed to external systems, like Gatekeeper
  * Or just respond with `AdmissionReview` Kubernetes object, so OPA can be plugged to Admission Controller

## Gatekeeper

* Provides more "Kubernetes-native" abstraction and functionalities over OPA
* Policies are stored as CRDs
* Constraint Templates
  * Enables you to provide a policy and pass variables during the valuation
* Gatekeeper provides a library for couple of policy types like PSPs
  * https://github.com/open-policy-agent/gatekeeper/tree/master/library
* Validating PSPs with web hooks might provide better UX, because apiserver rejects the object instantly, not only then when pod starts
* Above-mentioned functionality does not use PSPs in the backend, but OPA implements PSP-like behavior 

# Conftest

> Write tests against structured configuration data using the Open Policy Agent Rego query language

* Policies can be bundled and pushed to registry (like Harbor) 
* Can be run in CI for getting feedback before applying manifests to cluster
* For example can be used to check Kubernetes API deprecations (https://github.com/swade1987/deprek8ion)

## Use Conftest and OPA for Dockerfile checks

* Conftest and OPA can be used to write for example security checks for Dockerfiles
* For example, check in CI that `latest` that tag is not used
* See https://blog.madhuakula.com/dockerfile-security-checks-using-opa-rego-policies-with-conftest-32ab2316172f for more information

## Resources
* TGI Kubernetes 119: Gatekeeper and OPA - https://www.youtube.com/watch?v=ZJgaGJm9NJE&
* https://www.openpolicyagent.org/
* https://play.openpolicyagent.org/
* https://github.com/open-policy-agent/gatekeeper
* https://github.com/open-policy-agent/opa
* https://github.com/open-policy-agent/conftest
* https://www.conftest.dev/
