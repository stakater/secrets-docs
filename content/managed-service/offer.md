# Stakater secrets management as a service

Stakater secrets management as a service comes with:

* [SLA](https://docs.stakater.com/content/saap-sla/saap-sla.html)
* [Support](https://docs.stakater.com/content/sre/support/support.html)

## Fully covered and effortless Vault operations

Stakater provides a fully configured and optimized Vault cluster. Stakater automates every part of the setup and running of Vault clusters. Stakater continuously monitors and upgrades infrastructure dependencies. All maintenance operations are included, such as Vault upgrades and OS patching. Stakater looks after the health, backups, upgrades, patching, access management, key rotation and monitoring.

## Secure by design

Stakater Vault will run in a _private_ cloud in your region of preference. Both cloud provider partners and Stakater are committed to ISO 27001. All traffic is always end-to-end TLS encrypted in transit to Vault. Vault data on the host is encrypted and cannot be accessed even if the host was compromised. Vault replicas are run in a completely isolated inaccessible environment. Vault will be set up as a single tenancy: Vault pods will not share a node with non-Vault pods. Policies can optionally be used to define under what conditions entities get access to secrets. Stakater will help set up such policies for different roles. Control groups can further be used to provide approval workflows. All plans include Auto-Unseal features. During initialization, the root token will be revoked after recovery keys have been distributed among key-holders and the authentication backend has been configured and validated. An operator along with a quorum of key-holders can re-generate the root token in case of emergency.

## Reduce cost and save time

Optimize your operations time while reducing costs. All plans include 100% free maintenance operations, such as Vault version upgrades, OS patching, log auditing, monitoring and custom alerts.

## Backup and restore

Reduced risk of losing data with automated backups stored in a cloud file storage, so they are always accessible to you. Restore your data at any point of time.

## High availability

Stakater Vault will run in high availability mode. Avoid cluster downtime by using industry standard high availability frameworks. Network-attached storage will be used to enable volumes to be re-bound to new pods should the original pods holding the volume claim go offline due to permanent node failure. Your Vault environment can tolerate local failures across one or more availability zones.

## Audit logs and metrics console

Vault's audit logs and metrics will be available. Logs will be written to a separate volume. Access your cluster information anytime in a user-friendly platform console.

## Namespace multi-tenancy

[Stakater Multi Tenant Operator](https://docs.stakater.com/mto/main/index.html) not only provides strong multi-tenancy, but also automatically manages RBAC for tenant secrets through tenant paths and policies.

## Kubernetes integration

Stakater platform supports three different ways to consume secrets from Vault: via a volume, via environment variables or via external-secrets. A change in secret value in Vault will automatically restart the application by [Stakater Reloader](https://github.com/stakater/Reloader). Pods are authenticated to Vault using the Kubernetes auth method.

## Requirements

Direct SSH access between client cloud and Stakater Vault cloud will be needed, preferably via site-to-site VPN.
