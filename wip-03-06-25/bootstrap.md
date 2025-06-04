# How to bootstrap a cluster

## Option 1: Using Crossplane

### Description
* There are 3 Compositions - xCluster, xMachine and xDnsZone.
* The CaaS repo serves as an app-of-apps for xCluster resources.
* Flow
    * A new cluster configuration is pushed to the repo
    * A new xCluster object is created in management cluster
    * The xCluster creation triggers 3 actions:
        1. Disposable-request to rancher's server that creates a new cluster
        2. Create xMachines for the cluster's master and infra nodes (resource is deployed on management cluster)
        3. Disposable-request to ? that creates a new configuration for this cluster in SRE

### Pros
* We're already familiar with crossplane http-provider
* It's easy to manage clusters using argocd this way

### Cons
* There's a need to create some sort of SRE server



## Option 2: Using Terraform

### Description
* Creating the cluster in Rancher using its official provider
* Creating an SRE equivalent for managing resources on management cluster
* Creating SRE file using a custom script and local-exec resource

### Pros
* Using official Rancher provider

### Cons
* Requires another technology, some sort of way to manage its runs, keep state and generally create a new environment
* Writing a custom script for SRE, combined with the complications of terraform integration, causes more pain than just maintaining an SRE server.
* Doesn't support argocd view



## Option 3: Using Python

### Description
* Running a python script through some CI
* Script flow:
    1. Registering to rancher through HTTP request
    2. Creating SRE file using the same logic of the other 2 options
    3. Creating a configuration file in the other new repo, like we do in the terraform option

### Pros
* Probably the easiest to execute
* Both other options include python anyway
* We're comfortable with gitlab-ci

### Cons
* Both of the other options saves state in some sort of a way
* Requires the new app-of-apps for management nodes
* Doesn't support argocd view

