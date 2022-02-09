---
order: 100
---
# Terminology

There are a few key concepts and terms that will be used throughout this documentation so it's recommended to become familiar with the brief explanations below and the additional details provided throughout the Overview section. 

- **HPC Hourglass:** The HPC Hourglass is the design methodology used by OpenFlightHPC for cluster design. It splits the concept of a _Platform_ and _Stack_ into independent components of a cluster. Allowing for different platforms and stacks to be combined to create the final cluster environment.
- **Platform:** A platform is the base of a cluster. The hardware, virtualisation or cloud back-end alongside the chosen operating system and domain services (such as user management, network configuration and security).
- **Stack:** A stack is the collection of configuration and tools that unify the platform into a complete cluster environment ready for users to execute their workloads on. This includes shared storage, cluster management and monitoring, HPC services and workload services. 
- **Controller:** A controller is the server used for deployment of the platform and stack. This server provides a centralised location to manage the cluster. 
- **Cluster Skeleton:** A cluster skeleton is the expected state of a platform in order for it to be ready for a stack. For more information see [the Cluster Skeleton breakdown](cluster-skeleton.md).

