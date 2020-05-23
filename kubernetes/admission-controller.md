# Admission Controllers

* Admission controllers checks whether request to apiserver should be allowed or not (after authentication and authorization)
* Dynamic admission controllers
  * can call validating service with webhook
  * this is beneficial for example in managed Kubernetes environments where API server can't be modified
  * configured with `ValidatingWebhookConfiguration`
* Mutating admission controller can modify workloads, for example inject init containers
* Kubernetes can send out `AdmissionReview` object to admission controller, which wraps the object which will be validated
* Admission controller need then respond with the validation results to the apiserver

## Resources

* TGI Kubernetes 119: Gatekeeper and OPA - https://www.youtube.com/watch?v=ZJgaGJm9NJE&
