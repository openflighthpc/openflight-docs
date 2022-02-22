---
order: 100
icon: book
---
# Common Terminology

Throughout the industry there are various terms and concepts that are mostly consistent. Find these terms below with some explanation alongside notes of key differences between various usages of the term. 

## Platform

A platform is the base of a cluster. The hardware, virtualisation or cloud back-end alongside the chosen operating system, network configuration and domain services (such as user management, network configuration and security).

## Stack

Also known as: Cluster

A stack usually refers to the entire HPC system - the platform, domain services and HPC services that create the environment for a user to create, analyse and run workloads.

In other contexts a stack can refer to the HPC services alone that turn a configured platform (OSes installed, network configured and domain services running) into a suitable environment for an end-user. 

## Controller 

Also known as: Management Node, Control Plane

A controller is the server used for deployment of the platform and stack. This server provides a centralised location to manage the cluster. It is usually attached to the cluster network and is detached from user management services such that only admins can access it.  

## HPC Hourglass

The HPC Hourglass is the design methodology used by OpenFlightHPC for cluster design. It splits the concept of a _Platform_ and _Stack_ (in terms of the top-level services for end-users) into independent components of a cluster. Allowing for different platforms and stacks to be combined to create the final cluster environment.

