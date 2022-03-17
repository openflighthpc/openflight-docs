---
order: 98
---

# Use Case

## Our Use Case

### Software Version

The v1.1.1 release of Omnia was used. With limited success getting the release to run OpenFlight forked and modified Omnia to add additional tags and customisation. This modification of Omnia can be found [here](https://github.com/openflighthpc/omnia/tree/dev/deployment-updates-v1.1.1).

### Hardware Used

The hardware used for this platform deployment was:

- 2 x Dell PowerEdge R740 - used for the `head` and `storage` nodes.
- 3 x Dell PowerEdge R6525 - used for the `controller`, `login` and `compute` nodes.
- 1 x Access Switch - configured with VLANs for separate `Data` and `Management` networks

## Our Setup Experience (Platform Deployment)

### Prepare the hardware

Prepare your hardware as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html) network diagram and ensure the following is configured as a minimum:

- Data (pri) network setup connecting to a NIC on every node + the controller.
- Management (mgt) network setup connecting the BMC on every node + a secondary nic on the controller.
- External (ext) network connected to a third nic on the controller
- iDRAC reset and configured to use dhcp and a user and password have been set consistently across all the nodes.
- BIOS reset and configured to use PXE boot on the interface connected to the data (pri) network

Make a note of the MAC address for both the BMC and interface connected to the data network for every node.

### Setup the Controller
Ensure the controller is correctly setup, this will usually require configuring the following as a minimum:
- Install the controller OS and ensure that sufficient partition sizes are configured.
- Disable `Selinux`, `Network Manager` and if using Centos 8 / Rocky 8, install the `network-scripts` package.
- Configure the data (pri) network interface with a static IP and correct subnet.
- Configure the management (mgt) network interface with a static IP and correct subnet.
- Configure the external (ext) network interface with either a static IP or dhcp as required by your external network.
- Ensure a suitable hostname and timezone is configured.

!!!info
We would recommend setting up the controller as a libvirt VM. This allowed us to create snapshots of the controller  at various stages and aided in recovery in the event of deployment issues.
!!!

### Install requirements

Install the required dependancies `python3` and `ansible`
```shell
yum install -y epel-release
yum install -y python3 git
pip3.6 install --upgrade pip
python3.6 -m pip install ansible
```
Clone the OpenFlight fork of Omnia
  ```shell
  git https://github.com/openflighthpc/omnia.git
  cd omnia
  git checkout release-1.1.1
  ```

### Configure the deployment
Configure the required options as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html). This will include as a minimum setting the correct parameters in the following files:

- `omnia_config.yml`
- `control_plane/input_params/base_vars.yml`
- `control_plane/input_params/login_vars.yml`

Configure the mapping files using the MAC addresses for your hardware by creating the following files in your omnia directory.

- `mapping_device_file.csv` - maps the BMC MAC address for each node to their respective management network IP.
```
00:00:0F:FD:E6:EC,10.11.1.1
00:00:4F:CB:EE:64,10.11.2.1
00:00:EC:6B:31:7E,10.11.3.1
00:00:7F:7A:16:79,10.11.4.1
```
- `mapping_host_file.csv` - maps the Data network MAC address for each node to an IP, short hostname and Omnia role. The short hostname should not contain the following characters: , (comma), . (period) or _ (underscore).
```
00:00:37:75:ED:92,10.10.1.1,head1,manager
00:00:1C:26:2D:40,10.10.2.1,login1,login_node
00:00:73:FE:2F:50,10.10.3.1,storage1,nfs_node
00:00:F1:79:D9:FA,10.10.4.1,node1,compute
```

Configure the full path to these files in `control_plane/input_params/base_vars.yml`

### Deploy Platform
- Run the ansible playbook
  ```shell
  cd control_plane
  ansible-playbook control_plane.yml
  ```

## Our Setup Experience (Stack Only)

When deploying the full stack, due to intermittent issues with OS deployment/build there was often a failure to deploy the stack. Even with the components up as expected there are some parts of the implementation that do not work as expected.

An OpenFlight fork of Omnia exists that does a minimal stack deployment of both the Kubernetes and SLURM services as configured by Dell. This stack method does not include the setting up of IPA or changing of system firewalls.

### Prepare Controller

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

### Configure Playbook

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

!!!info Nodes Behind a Gateway / Controller Outside of Network
If network forwarding to the Internal (Cluster Primary) Network is not configured, Ansible can utilise SSH Tunnels via the headnode as follows:
```
node01    ansible_host=10.10.0.1 ansible_ssh_common_args='-o ProxyJump=10.10.0.1'
```
If utilising the above, the remote user may need to be specified in the ProxyJump argument (e.g. `ProxyJump-root@10.10.0.1`) and `sshpass` may be required on the _Deployment Server_. It may also be necessary to disable SSH Host Key Checking with `export ANSIBLE_HOST_KEY_CHECKING=false`.
!!!

!!!warning SSH with Password
Using password authentication with SSH proxies introduces many teething issues with Ansible therefore it is recommended that all hosts in the _Cluster Skeleton_ have the public SSH key from the _Deployment Server_ in the root user's authorized_keys file.
!!!

### Deploy Stack

- Run Playbook
  ```shell
  ansible-playbook -i myclusterinventory --extra-vars "storage=TYPE user_mgmt=TYPE stack=omnia" main.yml
  ```
