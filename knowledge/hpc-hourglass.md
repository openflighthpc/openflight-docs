---
icon: hourglass
---
# HPC Hourglass

The HPC Hourglass separates the concept of a cluster into two sections, the platform and the stack. Where a platform consists of the base components of a system and a stack comprises of the HPC services.

The breakdown of a cluster into these stages allows for modular cluster building and narrowing down specific implementation considerations ahead of building a cluster. 

## Platform 

### 1. Hosts, Disks & Networking 

_Examples: Bare Metal, Libvirt/KVM/Ovirt, VMWare, OpenStack, AWS, Azure_

This area covers the choice of hardware configuration for the cluster. It may be that physical servers are used as-is or as a base for virtualisation services. It's also an option to use public and private cloud services to probide the basis for hosts, disks and networks. 

When selecting the base for hosts, disks and networks it's worth considering:
- The desired size of the cluster (smaller workloads may be better suited to cloud platforms)
- Storage requirements for the workloads (including considerations of data integrity, redundancy and archival)
- Network performance requirements (for example, MPI jobs will likely benefit from high-speed interconnect)
- The types of nodes required, such as:
    - **Gateway** - A gateway node can be used to provide access to the external network and Internet for the cluster, keeping most of the cluster isolated from the world which can improve security.
    - **Master** - A master node can be used as a host of services, especially if these services are going to be virtualised or containerised. Multiple master nodes can be configured with high availability to ensure that services remain available regardless of hardware issues. 
    - **Infra** - An infra node can be used to provide domain and cluster services (e.g. DNS, User Management, Queue Systems). 
    - **Storage** - A storage node can serve network storage solutions to systems on the network. It could have some sort of storage array connected to it which would provide large and resilient storage.
    - **Login** - A login node can provide access to the HPC platform and be the central system that users access to run applications. How users will access the system should be considered, usually this will be SSH and some graphical login service, such as, VNC.
    - **Compute** - Compute nodes are usually used for running HPC applications that are queued through a job scheduler. Additionally, these can be used for VM deployments (via software like OpenStack) or other computational uses. Compute nodes usually have large amounts of cores and memory as well as high bandwidth interconnect (like Infiniband).
    - **Special Purpose** - Some compute nodes may feature a particular specification to be used for a particular job, or stage in your workflow. Examples may include nodes with more memory, larger amounts of local scratch storage, or GPU/FPGA devices installed.

The above types are not strict. Services can be mixed, matched and moved around to create the desired balance and distribution of services and functions for the cluster.

### 2. OS

_Examples: CentOS 7, RHEL 7, Rocky 8, RHEL 8_

The Operating System choice is dependent on the desired workflow and services for the cluster. It is worth considering the end-user's requirements for a workload and identify the most suitable operating system for this. 

RedHat and it's derivatives are often used in clusters as they have a lot of support (both commercially and from the community) and software availability.

### 3. Deployment Tool

_Examples: Cobbler/Kubernetes, OpenStack, xCAT/Warewulf, ARM Template, AWS CloudFormation, OpenStack Heat Template, Redhat CloudForms, Terraform_

Deployment tools can assist in deploying many systems with varying profiles dependent on grouping. This allows for centralised configuration & management of the cluster build process while minimising the labour required to configure many systems in a similar manner. 

### 4. Domain Services

#### 4.1. IP Allocation

_Examples: Static, DHCP_

IP Allocation covers the configuration of network interfaces on the platform. Ensuring that nodes have suitable IPs on the chosen networks, correct network routing to reach the external network and NTP contacting a suitable endpoint. 

#### 4.2. DNS

_Examples: /etc/hosts, DNSmasq, IPA_

Name resolution in the cluster is important for cluster communication. This can either be handled with a simple configuraiton (through adding host entires to `/etc/hosts` across the cluster) or by configuring a centralised service (such as DNSmasq or IPA) to provide hostname information to the cluster.

It is worth considering:
- The Fully-Qualified Domain Name to be used by the system, for example `mycluster.local.network`
- The sub-domains for the networks (if multiple networks are to be used), for example `pri.mycluster.local.network`

#### 4.3. User Management

_Example: NIS, IPA_

User authentication is usually performed in a server/client setup inside the HPC environment due to the unnecessary overhead of manually maintaining /etc/passwd on a network of nodes.

Some options for User Management:
- **NIS** - The Network Information Service (NIS) is a directory service that enables the sharing of user and host information across a network.
- **FreeIPA** - FreeIPA provides all the information that NIS does as well as providing application and service information to the network. Additionally, FreeIPA uses directory structure such that information can be logically stored in a tree-like structure. It also comes with a web interface for managing the solution. 
- Connecting to an **externally-managed** user-authentication service (e.g. LDAP, active-directory). This option is not recommended, as a large HPC cluster can put considerable load on external services. Using external user-authentication also creates a dependancy for your cluster on another service, complicating troubleshooting and potentially impacting service availability.

#### 4.4. Storage Filesystem

_Examples: Ceph, Lustre, GPFS, BGFS_

Storage Filesystems are an important consideration to ensure that user data is safe and accessible. It is worth considering the level of redundancy provided by storage solutions as well as the performance of the filesystem types.

Some considerations for Storage Filesystems:
- What data will need to be centrally stored?
- Where will data be coming from?
  - Are source files created within the HPC network or do they exist in the external network?
  - Will compute nodes be writing out logs/results from running jobs?
  - Where else might data be coming from?
  - Is scratch space needed?
- What level of redundancy/stability is required for the data?
- How will the data be backed up?
  - Will there be off-site backups?
  - Should a separate storage medium be used?
  - Does all the data need backing up or only certain files?
  - For how long will we keep the data, and any backups created?
- What are my disaster recovery and business continuity plans?
  - If the storage service fails, how will my users continue working?
  - How can I recreate the storage service if it fails? 
  - How long will it take to restore any data from backups?

#### 4.5. Storage Share

_Examples: NFS, Lustre_

The Storage Share determines how the Storage Filesystem is accessed by nodes in the cluster. The choice of share can impact the performance of data read and write. 

Some things to consider:
- Network performance required for workloads
- Mountpoints to use (e.g. one for users, one for software, one for project data) 

## Stack

### 1. Management

_Examples: Ganglia, Nagios, Zabbix, Grafana/Prometheus_

For monitoring and identifying issues with the cluster it is recommended to have some form of management software configured that'll collect statistics and perform checks on the nodes. There are many options of both active and passive management tools which can be configured for the cluster, consider the options out there alongside the functionality provided.

### 2. HPC Services

_Examples: Conda, EasyBuild, Modules, Singularity, Spack, Docker_

HPC Services determine how software and libraries are acquired for users. There are multiple options from software ecosystems (like Conda and EasyBuild) to containerised solutions (like Docker and Singularity). While a lot of software can be installed from precompiled binaries (or directly compiled for the host OS) it may be that containerised solutions are required for certain workloads. 

### 3. Workload

_Examples: Torque, SLURM, Kubernetes, Jupyter_

Workload management tools allow for the allocation of resources to users and can additionally provide fine-tuned resource allocation with quotas and resource separation. 

It is worth considering the variety of workloads that the cluster will be used for in order to identify the most suitable workload manager.

