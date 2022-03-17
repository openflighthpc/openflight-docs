---
label: OpenFlight
icon: package
---

## Overview

The OpenFlight Stack is a collection of tools for streamlining the management of a research environment.

The stack includes:
- SLURM for managing compute queues
- Software environment tools (allowing for user-level installation of research software from multiple sources) 
- Desktop session manager

## Deployment

### Prepare Controller

- Install Ansible
- Clone the OpenFlight Stacks Playbook
  ```shell
  git clone https://github.com/openflighthpc/openflight-ansible-playbook
  ```
- Navigate into the repo
  ```shell
  cd openflight-ansible-playbook
  ```

### Configure Playbook

- Create Inventory File (IP specification is only required if DNS is not setup on the _Deployment Server_ for the IPs)
  ```shell
  cat << EOF > myclusterinventory
  [head]
  head1    ansible_host=10.10.0.1

  [nodes]
  node01    ansible_host=10.10.1.1
  node02    ansible_host=10.10.1.2
  node03    ansible_host=10.10.1.3
  node04    ansible_host=10.10.1.4
  EOF
  ```
- Set the variables in `group_vars/all`:
  - `cluster_name`: The name to be assigned to the cluster

!!!info SSH Tunneling with Ansible
If network forwarding to the Internal (Cluster Primary) Network is not configured, Ansible can utilise SSH Tunnels via the headnode as follows:
```
node01    ansible_host=10.10.0.1 ansible_ssh_common_args='-o ProxyJump=10.10.0.1'
```
If utilising the above, the remote user may need to be specified in the ProxyJump argument (e.g. `ProxyJump-root@10.10.0.1`) and `sshpass` may be required on the _Deployment Server_. It may also be necessary to disable SSH Host Key Checking with `export ANSIBLE_HOST_KEY_CHECKING=false`.
!!!

!!!warning SSH with Password
Note: Using password authentication with SSH proxies introduces many teething issues with Ansible therefore it is recommended that all hosts in the _Cluster Skeleton_ have the public SSH key from the _Deployment Server_ in the root user's authorized_keys file.
!!!

### Deploy Stack

- Run Playbook
  ```shell
  ansible-playbook -i myclusterinventory stack-only.yml
  ```
