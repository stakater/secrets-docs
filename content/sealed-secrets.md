# Sealed Secrets

The [SealedSecrets](https://github.com/bitnami-labs/sealed-secrets) controller solves the problem of storing Kubernetes secret data securely by encrypting the configurations. It can only be decrypted by sealed secret controller running in cluster.

## Architecture

`SealedSecrets` is composed of two Kubernetes components:

* `Controller`: A cluster-side component for data decryption.
* `Kubeseal`: A client-side utility for data encryption. It uses asymmetric cryptography methods for data encryption and kubeconfig to communicate with the cluster.

## Pre-Requisites

You need to have `kubeseal` installed on your local machine. You can get the latest release from [Sealed Secret Releases](https://github.com/bitnami-labs/sealed-secrets/releases).

## Usage

### Create K8s sealed secret

Lets create a sample k8s secret that will be used for MySQL:

1. Create the secret as a normal Kubernetes secret resource:

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysql-secrets
      namespace: gabbar-dev
    data:
      # base64 encoded "root"
      USERNAME: cm9vdA==
      # base64 encoded "@mysqlpassword"
      USER_PASSWORD: QG15c3FscGFzc3dvcmQ=
      # base64 encoded "test-database"
      DATABASE: dGVzdC1kYXRhYmFzZQ==
    ```

1. Save it in a file named `secret-mysql.yaml`. This file should not be pushed to source control as it is easily de-codable.

1. Install kubeseal and your kubeconfig should be pointing to your SAAP cluster

1. Sealed secrets controller is running in `stakater-sealed-secrets` namespace and sealed secrets service name is `sealed-secrets`, so you need to run:

    ```sh
    kubeseal --controller-name=sealed-secrets --controller-namespace=stakater-sealed-secrets --format yaml < SECRET_FILE 
    ```

    where:

    * `SECRET_FILE`: the name of the yaml file containing the k8s secret
    * `SEALED_SECRET`: the name of the yaml file that will contain the sealed secret

1. Add and commit this sealed secret to source control

1. Remove the original secret

## For Dynamic Test Environment

To use secrets in dynamic test environments (for PR environment), you need to seal secret with `cluster-wide` scope so that it can be decrypted by any namespace. Since PR namespaces are dynamic and tied to the lifecycle of PR, `cluster-wide` scope make sure that it can be decrypted by dynamic namespaces.

To create a sealed secret in Dynamic Test Environments:

1. Generate a `SealedSecret` resource:

    ```sh
    kubeseal --controller-name=sealed-secrets --controller-namespace=stakater-sealed-secrets --format yaml --scope cluster-wide < secret-mysql.yaml 
    ```

    This will generate sealedsecret resource output:

    ```yaml
    apiVersion: bitnami.com/v1alpha1
    kind: SealedSecret
    metadata:
      annotations:
        sealedsecrets.bitnami.com/cluster-wide: "true"
      creationTimestamp: null
      name: mysql-secrets
    spec:
      encryptedData:
        DATABASE: AgBLmwvAw...
        USER_PASSWORD: AgCkxffaV...
        USERNAME: AgAkGib0a...
      template:
        metadata:
          annotations:
            sealedsecrets.bitnami.com/cluster-wide: "true"
          creationTimestamp: null
          name: mysql-secrets
    ```

1. Add a `sealedSecret` block in the Helm values present in `deploy/values.yaml` in your application repository and copy/paste the key values from the resource:

    ```yaml
      sealedSecret:
        enabled: true
        annotations:
          sealedsecrets.bitnami.com/cluster-wide: "true"
        files:
        - name: mysql-secrets
          encryptedData:
            DATABASE: AgBLmwvAw...
            USER_PASSWORD: AgCkxffaV...
            USERNAME: AgAkGib0a...
    ```

## For Dev Environment

To generate sealedsecrets for dev environment:

1. Run the following command:

    ```sh
    kubeseal --controller-name=sealed-secrets --controller-namespace=stakater-sealed-secrets --format yaml  < secret-mysql.yaml 
    ```

    This will generate sealedsecret resource output:

    ```yaml
    apiVersion: bitnami.com/v1alpha1
    kind: SealedSecret
    metadata:
      creationTimestamp: null
      name: mysql-secrets
      namespace: gabbar-dev
    spec:
      encryptedData:
        DATABASE: AgBLmwvAw...
        USER_PASSWORD: AgCkxffaV...
        USERNAME: AgAkGib0a...
      template:
        metadata:
          creationTimestamp: null
          name: mysql-secrets
          namespace: gabbar-dev
    ```

1. Add a `sealedSecret` block in the Helm values present in `<tenant>/<application>/<env>/values.yaml` in the GitOps-config repository and copy/paste key values from the generated output:

    ```yaml
      sealedSecret:
        enabled: true
        annotations: ""
        files:
        - name: mysql-secrets
          encryptedData:
            DATABASE: AgBLmwvAw...
            USER_PASSWORD: AgCkxffaV...
            USERNAME: AgAkGib0a...
    ```

### Consuming Secret in Application using Environment Variables

When consuming the sealed secret in the application using environment variables, you need to add the `env:` field in the values file, for example if you want to use environment variables from above MySQL secret, in values file, replace:

```yaml
  env: []
```

with:

```yaml
  env:
  - name: MYSQL_USERNAME
    valueFrom:
      secretKeyRef:
        name: mysql-secrets
        key: USERNAME
  - name: MYSQL_PASSWORD
    valueFrom:
      secretKeyRef:
        name: mysql-secrets
        key: USER_PASSWORD
```
