---
order: 99
---

!!!warning
This documentation will soon be broken down into modular chunks for the various components that make up a stack
!!!

+++ OpenFlight

## Prepare Controller

- Install Ansible
- Clone the OpenFlight Stacks Playbook
  ```shell
  git clone https://github.com/openflighthpc/stacks-playbook
  ```
- Navigate into the repo
  ```shell
  cd stacks-playbook
  ```

## Configure Playbook

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
- Set the variables for your chosen storage (`roles/storage-TYPE/vars/main.yml`)
- Set the variables for your chosen user management solution (`roles/users-TYPE/vars/main.yml`)

Note: If network forwarding to the Internal (Cluster Primary) Network is not configured, Ansible can utilise SSH Tunnels via the headnode as follows:
```
node01    ansible_host=10.10.0.1 ansible_ssh_common_args='-o ProxyJump=10.10.0.1'
```
If utilising the above, the remote user may need to be specified in the ProxyJump argument (e.g. `ProxyJump-root@10.10.0.1`) and `sshpass` may be required on the _Deployment Server_. It may also be necessary to disable SSH Host Key Checking with `export ANSIBLE_HOST_KEY_CHECKING=false`.

Note: Using password authentication with SSH proxies introduces many teething issues with Ansible therefore it is recommended that all hosts in the _Cluster Skeleton_ have the public SSH key from the _Deployment Server_ in the root user's authorized_keys file.

## Deploy Stack

- Run Playbook
  ```shell
  ansible-playbook -i myclusterinventory --extra-vars "storage=TYPE user_mgmt=TYPE stack=openflighthpc" main.yml
  ```

+++ Omnia

For further information on deploying Dell's Omnia stack, see their [installation documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA.html).

Alternatively, OpenFlight maintains a fork of Omnia along with a guide - see the "OpenFlight Omnia" tab for more information.

+++ OpenFlight Omnia

## Prepare Controller

- Install Ansible
- Clone Repository
  ```shell
  git clone https://github.com/openflighthpc/stacks-playbook
  ```
- Navigate into the repo
  ```shell
  cd stacks-playbook
  ```
- Initialise the submodules
  ```shell
  git submodule update --init
  ```

## Configure Playbook

- Create Inventory File (IP specification is only required if DNS is not setup on the _Deployment Server_ for the
IPs)
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
  - `compute_ip_range`: The IP network and netmask for the Internal (Cluster Primary) Network
  - `head_ip`: The IP address of the head node on the Internal (Cluster Primary) Network
  - [OPTIONAL] `storage_ip`: The IP address of the storage node on the Internal (Cluster Primary) Network. If left blank then the head node will be configured as a storage node
- Set the variables for your chosen storage (`roles/storage-TYPE/vars/main.yml`)
- Set the variables for your chosen user management solution (`roles/users-TYPE/vars/main.yml`)

Note: If network forwarding to the Internal (Cluster Primary) Network is not configured, Ansible can utilise SSH T
unnels via the headnode as follows:
```
node01    ansible_host=10.10.0.1 ansible_ssh_common_args='-o ProxyJump=10.10.0.1'
```
If utilising the above, the remote user may need to be specified in the ProxyJump argument (e.g. `ProxyJump-root@1
0.10.0.1`) and `sshpass` may be required on the _Deployment Server_. It may also be necessary to disable SSH Host
Key Checking with `export ANSIBLE_HOST_KEY_CHECKING=false`.

Note: Using password authentication with SSH proxies introduces many teething issues with Ansible therefore it is
recommended that all hosts in the _Cluster Skeleton_ have the public SSH key from the _Deployment Server_ in the r
oot user's authorized_keys file.

## Deploy Stack

- Run Playbook
  ```shell
  ansible-playbook -i myclusterinventory --extra-vars "storage=TYPE user_mgmt=TYPE stack=omnia" main.yml
  ```

+++ StackHPC OpenHPC

For further information on deploying StackHPC's OpenHPC stack, see their [README](https://github.com/stackhpc/ansible-role-openhpc/blob/master/README.md).

Alternatively, OpenFlight maintains a fork of StackHPC's OpenHPC stack along with a guide - see the "OpenFlight OpenHPC" tab for more information.

+++ OpenFlight OpenHPC

## Prepare Controller

- Install Ansible
- Clone Repository
  ```shell
  git clone https://github.com/openflighthpc/stacks-playbook
  ```
- Navigate into the repo
  ```shell
  cd stacks-playbook
  ```
- Initialise the submodules
  ```shell
  git submodule update --init
  ```

## Configure Playbook

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
- Set the variables for your chosen storage (`roles/storage-TYPE/vars/main.yml`)
- Set the variables for your chosen user management solution (`roles/users-TYPE/vars/main.yml`)

Note: If network forwarding to the Internal (Cluster Primary) Network is not configured, Ansible can utilise SSH T
unnels via the headnode as follows:
```
node01    ansible_host=10.10.0.1 ansible_ssh_common_args='-o ProxyJump=10.10.0.1'
```
If utilising the above, the remote user may need to be specified in the ProxyJump argument (e.g. `ProxyJump-root@1
0.10.0.1`) and `sshpass` may be required on the _Deployment Server_. It may also be necessary to disable SSH Host
Key Checking with `export ANSIBLE_HOST_KEY_CHECKING=false`.

Note: Using password authentication with SSH proxies introduces many teething issues with Ansible therefore it is
recommended that all hosts in the _Cluster Skeleton_ have the public SSH key from the _Deployment Server_ in the r
oot user's authorized_keys file.

## Deploy Stack

- Run Playbook
  ```shell
  ansible-playbook -i myclusterinventory --extra-vars "storage=TYPE user_mgmt=TYPE stack=openhpc" main.yml
  ```

+++