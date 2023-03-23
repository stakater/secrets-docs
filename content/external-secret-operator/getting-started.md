# Getting Started with External Secret Operator

To use External Secret Operator for your application, you need to perform the following steps:

- [Login to Vault](../vault.md#authentication)
- [Add secrets in Vault](../vault.md#create-secrets)
- Configure `ExternalSecrets` in Helm values

## Prerequisite

Tenant `CustomResource` should be using template named as `tenant-vault-access`:

```yaml
apiVersion: tenantoperator.stakater.com/v1alpha1
kind: Tenant
metadata:
  name: gabbar
spec:
  users:
    owner:
    - user1
    - user2
  quota: medium
  namespacetemplate:
    templateInstances:
    - spec:
        template: tenant-vault-access
        sync: true
```

## Configure ExternalSecrets in Helm values

In your `deploy/values.yaml`, enable `externalSecret` and provide details of the secret path in Vault:

```yaml
externalSecret:
    enabled: true
    secretStore:
      name: tenant-vault-secret-store
    files:   
     inventory-postgres: #Name of Kubernetes Secret
      data:
        postgresql-password: #Name of Kubernetes Secret Key
          remoteRef:
            key: inventory-postgres #Name of Vault Secret
            property: postgresql-password #Name of Vault Secret Key
```
