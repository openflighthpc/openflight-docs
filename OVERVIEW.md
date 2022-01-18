# Overview 

This repository of documentation provides guidance on deploying platforms and configuring stacks in order to create a functional HPC environment. 

# Providers

There are a few different platform & stack managers utilised in this repo. A comparison of them can be found below

## Platform

|                           | Omnia | OpenFlight | OpenHPC |
| :-----------------------: | :---: | :--------: | :-----: |
| **Deploy OS to Hardware** |   Y   |     Y      |    ~    |
| **Deploy OS to Cloud**    |   N   |     Y      |    N    |
| **Configure Networking**  |   Y   |     Y      |    ~    |
| **CentOS 7 Support**      |   Y   |     Y      |    Y    |
| **Rocky 8 Support**       |   Y   |     Y      |    Y    |

Key:
- Y: Supported
- ~: Partial Support (may require third-party tools or additional content) 
- N: Not Supported

## Stack

|                     | Omnia | OpenFlight | OpenHPC |
| :-----------------: | :---: | :--------: | :-----: |
| **Shared Storage**  |   Y   |     Y      |    N    |
| **User Management** |   Y   |     Y      |    N    |
| **Queue System**    |   Y   |     Y      |    Y    |
| **HPC Environment** |   ~   |     Y      |    Y    |

Key:
- Y: Supported
- ~: Partial Support (may require third-party tools or additional content) 
- N: Not Supported

# Key Concepts 

## Deployment Server

This server is where the deployment of the _HPC Stack_ is performed from.

A _Deployment Server_ is assumed to:
- Be running CentOS 7 or Rocky 8 Linux 
- Have network access to all the systems in the _Cluster Skeleton_
- Have passwordless SSH to root on the servers

## Cluster Skeleton

A _Cluster Skeleton_ is a minimally configured platform providing a base for the desired _HPC Stack_ to be deployed onto.

A _Cluster Skeleton_ is assumed to consist of:
- 1 OS (CentOS 7 or Rocky 8 Linux) with:
    - External Network Access
    - Internal (Cluster Primary) Network Access
    - Network Storage (formatted & exported)
- X OSes (CentOS 7 or Rocky 8 Linux) with:
    - Internal (Cluster Primary) Network Access

Examples for deploying a _Cluster Skeleton_ on multiple networks can be found in `platform/`.

**It is recommended to have a restoration point for the _Cluster Skeleton_, this will ensure that the base platform can be recovered in the event of _HPC Stack_ issues or if deploying a different _HPC Stack_ to the _Cluster Skeleton_ - For more information see `platform/disaster-recovery-change-control.md`.**

## HPC Stack

A _HPC Stack_ provides the various services and tools for running HPC applications, such as:
- User Management (e.g. IPA, NIS, LDAP)
- Queue Management (e.g. SLURM, SGE, PBS)
- HPC Application Management (e.g. Repos, EasyBuild, Conda)

