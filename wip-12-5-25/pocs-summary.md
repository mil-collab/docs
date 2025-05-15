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
* Crossplane's HTTP-Provider seems solid.
* We don't have to touch go at all for this part of the project.
* There might be some design requirements from our EC2 implementation.

### Next Steps
* Figure out how to configure max backoff
* List requires features/constraints of the EC2 API to guide its implementation.
* Try reading from a secret
* Register to Rancher using it


## Cluster API

### Our POC
* Played with MachineDeployment and MachineSet CRDs.
* Created controller for a fake-machine.
* Struggled with deploying the Cluster CRD and bootstrapping the provider.

### Conclusion
* CAPI does more than we thought. If we want to use it as a datasource that reflects deployed crossplane CRDs, we'll have to 'cheat' the CAPI controller and make some bypasses - just to pass some of the requirements of a CAPI Provider.
* Even if our architecture doesn't depend on CAPI, we'll be able to integrate it with our system in the future. We don't have to rely on it right now.

### Next Steps
* Pass the new design through some DRs to approve abandoning CAPI.


## Cluster-Autoscaler gRPC Provider

### Our POC
* Investigated the ability to use gRPC and Protobufs to create a custom API for the K8s-native cluster-autoscaler
* Compiled the .proto files of K8s and the provider to python code
* Understood the format and ease of usage of Protobufs
* Created a gRPC server build on the compiled code, *in python*.

### Conclusion
* We can implement the cluster-autoscaler API in python following the Protobuf protocol.
* We get a boilerplate of the API out-of-the-box.

### Next Steps
* Implement an autoscaler demo using a small implementation of this server
