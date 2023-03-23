# External Secret Operator

A Kubernetes secret contains sensitive information decoupled from the application code and stored in key-value pairs. The Secret object provides a declarative API that makes it easy for application pods to access secret data. Kubernetes secrets however do not support storing or retrieving secret data from external secret management systems, such as HashiCorp Vault.

_External Secrets_ solves this problem by providing access to secrets stored externally. It does this by adding an `ExternalSecret` object to Kubernetes using a [CustomResourceDefinition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/). Developers specify the secret management system as `backendType` and the access properties in the manifest.

SAAP uses the _External Secrets Operator_ to integrate HashiCorp Vault. The operator synchronizes secrets from external APIs into Kubernetes with the help of custom API resources: `ExternalSecret`, `SecretStore` and `ClusterSecretStore`.

## Secret Store

A secret store defines how to fetch the data. It defines the provider, such as Vault, its server address, the path for secrets, and its authentication method, e.g. service account bound with Vault role and policy:

```yaml
apiVersion: external-secrets.io/v1alpha1
kind: SecretStore
metadata:
    name: tenant-vault-secret-store
spec:
    provider:
    vault:
        server: "http://vault.stakater-vault:8200"
        path: "gabbar/kv"
        version: "v2"
        auth:
        kubernetes:
          mountPath: "kubernetes"
          role: "gabbar-dev"
          serviceAccountRef:
            name: "tenant-vault-access"
            namespace: "gabbar-dev"
```

## External Secret

An external secret declares what data to fetch. It has a reference to a SecretStore which knows how to access that data:

```yaml
apiVersion: external-secrets.io/v1alpha1
kind: ExternalSecret
metadata:
  name: example
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: tenant-vault-secret-store
    kind: SecretStore
  data:
  - secretKey: secret-key-to-be-managed
    remoteRef:
      key: provider-key
      property: provider-key-property
  dataFrom:
  - key: remote-key-in-the-provider
```

## Cluster Secret Store

A cluster secret store is a global, cluster-wide `SecretStore` that can be referenced from all namespaces. It is used for secrets that need to be distributed across all namespaces.
