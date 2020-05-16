# Secrets

* `Secret` API object
  * stores secrets as base64 encoded in etcd
  * encryption comes with extra-configuration or integration
  * plain-text values can be used with "stringData" attribute

* Encryption at rest (with `EncryptionConfiguration` API object)
  * Defines a key which encrypts and decrypts Secrets in etcd
  * however, the encryption key is in plain-text in apiserver
    * this is a concern if apiserver and etcd are co-hosted on a same node
  * keys must rotated

* KMS (Envelope encryption)
  * Data is encrypted with Data encyption key (DEK)
  * DEK is encrypted with Key Encryption Key (KEK)
  * Data and and enrypted DEK are stored side-by-side
  * When data is decrypted, a call to KMS provider is done to decrypt the DEK
    * so the secret is never transmitted to KMS provider
  * Most usable with cloud providers with KMS service

* External provider
  * Hashicorp Vault
  * Integration on
    * platform level
        * Vault Injection
          * creates init container for fetching secrets from Vault
            * uses mutating webhook to inject Vault init container
          * or run as sidecar, and fetch if secrets secrets are modified
    * application level

* Sealed secrets
  * mostly solves the problem of keeping secrets safe *outside of Kubernetes* 
  * plain-text copies of sealed secret controller secrets and secrets itself are stored to `etcd`. So encrypting `etcd` need to be solved somehow

* csi-secret-driver
  * mount secrets/keys/certs to pod using a CSI volume
  * still in experimental state

## Resources

* TGI Kubernetes 113: Kubernetes Secrets Take 3 - https://www.youtube.com/watch?v=an9D2FyFwR0
* https://github.com/bitnami-labs/sealed-secrets 
* https://github.com/kubernetes-sigs/secrets-store-csi-driver
* https://www.hashicorp.com/blog/injecting-vault-secrets-into-kubernetes-pods-via-a-sidecar/
