# Cluster configuration guidelines

Guidelines for cluster configuration:

* Control plane
    * 3 control plane nodes
    * Vault will not be scheduled on the control plane in accordance with general Kubernetes community recommendations
    * Vault does not place unusual demands on the control plane relative to other general workloads
* Worker nodes
    * A minimum of 3 worker nodes is needed to enable failure tolerance
    * The internal database that Vault uses is optimized for SSD drives
    * CPU and storage performance requirements will depend on the customer's exact use profile
        * Use Vault metrics to understand the load
* Infra nodes
    * 3 infrastructure nodes
    * Traffic routing and monitoring pods will be hosted on the infra nodes

## Recommended cluster configuration

Stakater recommends this cluster configuration:

* Two Kubernetes clusters to host four Vault clusters for sake of HA for two sites
* Four Vault clusters (two prod and two non-prod) is the minimum setup to enable High Availability (HA) and Disaster Recovery:
    * Two Vault clusters running on one prod Kubernetes cluster:
        * One primary active prod Vault
        * One secondary passive non-prod Vault (DR)
    * Two Vault clusters running on one non-prod Kubernetes cluster:
        * One primary active non-prod Vault
        * One secondary passive prod Vault (DR)
* Each Kubernetes cluster will have three control plane nodes and six worker/infra nodes
* Each node (control plane/worker/infra) will be 4 vCPUs x 16 GB RAM x 100 GB disk with minimum 3000 IOPS
* Each Vault cluster will be three nodes that will run on three worker/infra nodes
* So, one Kubernetes cluster will be 36 vCPUs x 144 GB RAM x 900 GB disk
* Each cluster will have two separate sharded ingresses
