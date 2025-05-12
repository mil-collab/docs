# Achievements during 11-12/5

## Crossplane

### Our POC
* Created a mock EC2 API
* Created a CRD - xMachine using compositions, that interacts with the API using nothing but the HTTP Provider
    - Configured CRUD operation.
    - Tested regular HTTP behavior.
    - Tested handling down-time in the API-server.
    - Rested handling slow response times.
    - Applied back-off.

### Conclusion
1. Crossplane's HTTP-Provider seems solid.
2. We don't have to touch go at all for this part of the project.
3. There might be some design requirements from our EC2 implementation.



## Cluster API

### Our POC
* Played with MachineDeployment and MachineSet CRDs.
* Created controller for a fake-machine.
* Struggled with deploying the Cluster CRD and bootstrapping the provider.

### Conclusion
1. CAPI does more than we thought. If we want to use it as a datasource that reflects deployed crossplane CRDs, we'll have to 'cheat' the CAPI controller and make some bypasses - just to pass some of the requirements of a CAPI Provider.
2. Even if our architecture doesn't depend on CAPI, we'll be able to integrate it with our system in the future. We don't have to rely on it right now.


## Cluster-Autoscaler gRPC Provider

