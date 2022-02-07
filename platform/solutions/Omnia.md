# Omnia (Dell) 

## Description

Omnia is a platform & stack deployment tool developed by Dell. It's specifically designed for deployment to Dell PowerEdge servers and provides all levels of deployment from provisioning iDRAC network information and configuring the BIOS to deploying kubernetes and SLURM across the cluster. 

## Quick Review

|    Category     |                                       Rating                                         |
| :-------------: | :----------------------------------------------------------------------------------: |
| Ease of Use     | :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star: :icon-star:           |
| Customisability | :icon-star-fill: :icon-star: :icon-star: :icon-star: :icon-star:                     | 
| Adding Nodes    | :icon-star: :icon-star: :icon-star: :icon-star: :icon-star:                          |
| Debuggability   | :icon-star-fill: :icon-star-fill: :icon-star: :icon-star: :icon-star:                |


- **Ease of Use:** How intuitive the solution is to obtain, configure & launch
- **Customisability:** The extent to, and simplicity of, which the solution can be modified to suit a desired deployment
- **Adding Nodes:** How achievable it is to add nodes to a live/existing system with the solution
- **Debuggability:** How possible it is to identify reasons for deployment failure

### Ease of Use

Gathering the tools required is pretty straightforward and the initial configuration of the variables for the playbook is also fairly well documented. The various steps to configure and run the tool are documented on the [Omnia website](https://dellhpc.github.io/omnia/) along with hardware requirements and explanations of other prerequisites the solution expects.

Additionally, a playbook is provided for configuring the "Control Plane" (similar to the OpenFlight concept of a Controller or OpenHPC's Master). 

### Customisability

The playbook is broken down into various roles for the different elements in the cluster. However, these roles have some interroped dependencies and, while there are tags on most tasks, it seems that some tagging is missing for roles so being able to voluntarily skip non-essential elements of the configuration can be tricky. 

### Adding Nodes

At OpenFlight, we had a fair bit of difficulty deploying the initial platform of a cluster that has led us to believe that it would be difficult to configure additional hardware further down the line. 

### Debuggability 

Initially it can be difficult to know exactly what went wrong in a run of the playbook, this is due to the debug output being disabled for most tasks so stdout & stderr from failing commands is not immediately available. Furthermore, the complexity added by the layers of the control plane deployment (namely the various layers of abstraction through usage of playbooks, kubernetes deployment containers and cobbler) means that locating exactly where something went wrong is unintuitive and can be buried behind abstractions of playbooks run within playbooks. 

## Detailed Review

The detailed review provides a breakdown of the OpenFlight experience in using this solution.

### Our Use Case

#### Software Version

The v1.1.1 release of Omnia was used. With limited success getting the release to run OpenFlight forked and modified Omnia to add additional tags and customisation. This modification of Omnia can be found [here](https://github.com/openflighthpc/omnia/tree/dev/deployment-updates-v1.1.1).

#### Hardware Used

The hardware used for this platform deployment was:

### Our Setup Experience

#### Prepare the hardware

Prepare your hardware as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html) network diagram and ensure the following is configured as a minimum:

- Data (pri) network setup connecting to a NIC on every node + the controller.
- Management (mgt) network setup connecting the BMC on every node + a secondary nic on the controller.
- External (ext) network connected to a third nic on the controller
- iDRAC reset and configured to use dhcp and a user and password have been set consistently across all the nodes.
- BIOS reset and configured to use PXE boot on the interface connected to the data (pri) network

Make a note of the MAC address for both the BMC and interface connected to the data network for every node.

#### Setup the Controller
Ensure the controller is correctly setup, this will usually require configuring the following as a minimum:
- Install the controller OS and ensure that sufficient partition sizes are configured.
- Disable `Selinux`, `Network Manager` and if using Centos 8 / Rocky 8, install the `network-scripts` package.
- Configure the data (pri) network interface with a static IP and correct subnet.
- Configure the management (mgt) network interface with a static IP and correct subnet.
- Configure the external (ext) network interface with either a static IP or dhcp as required by your external network.
- Ensure a suitable hostname and timezone is configured.

#### Install requirements

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

#### Configure the deployment
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

#### Deploy Platform
- Run the ansible playbook
  ```shell
  cd control_plane
  ansible-playbook control_plane.yml
  ```

### Conclusion

In conclusion, it took a lot of battling with hardware and resetting of the control plane when changes were made or failure conditions were hit for various different reasons in order to get the initial OS deployed to the nodes. 
