---
order: 99
---
# Cluster Skeleton

A _Cluster Skeleton_ is a minimally configured platform providing a base for the desired _HPC Stack_ to be deployed onto.

A _Cluster Skeleton_ is assumed to consist of:
- "Controller" - 1 OS (CentOS 7 or Rocky 8 Linux) with: 
    - External Network Access
    - Internal (Cluster Primary) Network Access
- "Head Node" - 1 OS (CentOS 7 or Rocky 8 Linux) with:
    - External Network Access
    - Internal (Cluster Primary) Network Access
    - Entries in `/etc/hosts` for itself and compute nodes
    - Network Storage (formatted & exported)
- "Compute Nodes" - X OSes (CentOS 7 or Rocky 8 Linux) with:
    - Internal (Cluster Primary) Network Access
    - Entries in `/etc/hosts` for the head and all compute nodes
- Consistent date & time across all nodes
- Centralised user management across head and compute nodes (IPA, NIS, etc)

**It is recommended to have a restoration point for the _Cluster Skeleton_, this will ensure that the base platform can be recovered in the event of _Stack_ issues or if deploying a different _Stack_ to the _Cluster Skeleton_ - For more information see [Disaster Recovery and Change Control](../platform/disaster-recovery-change-control.md).**
