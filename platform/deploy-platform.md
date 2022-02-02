---
order: 100
---
# Deploy Platform

## Overview

This documentation explains the initial configuration required for a platform in order to begin deployment of a cluster skeleton.

## How To

+++ Hardware (Omnia)

## Prepare the hardware

Prepare your hardware as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html) network diagram and ensure the following is configured as a minimum:

- Data (pri) network setup connecting to a NIC on every node + the controller.
- Management (mgt) network setup connecting the BMC on every node + a secondary nic on the controller.
- External (ext) network connected to a third nic on the controller
- iDRAC reset and configured to use dhcp and a user and password have been set consistently across all the nodes.
- BIOS reset and configured to use PXE boot on the interface connected to the data (pri) network

Make a note of the MAC address for both the BMC and interface connected to the data network for every node.

## Setup the Controller
Ensure the controller is correctly setup, this will usually require configuring the following as a minimum:
- Install the controller OS and ensure that sufficient partition sizes are configured.
- Disable `Selinux`, `Network Manager` and if using Centos 8 / Rocky 8, install the `network-scripts` package.
- Configure the data (pri) network interface with a static IP and correct subnet.
- Configure the management (mgt) network interface with a static IP and correct subnet.
- Configure the external (ext) network interface with either a static IP or dhcp as required by your external network.
- Ensure a suitable hostname and timezone is configured.

## Install requirements

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

## Configure the deployment
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

## Deploy Platform
- Run the ansible playbook
  ```shell
  cd control_plane
  ansible-playbook control_plane.yml
  ```

+++ Azure (OpenFlight)

## Prepare Controller

- [Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=dnf)
- [Authenticate Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli#how-to-sign-into-the-azure-cli)
- Clone the OpenFlight Platform Examples
  ```shell
  git clone https://github.com/openflighthpc/platforms-examples
  ```
- Navigate into the repo and Azure directory
  ```shell
  cd platforms-example/azure
  ```

==- :icon-duplicate: Importing an Image

OpenFlight provides images for various cloud platforms, to see the available images for Azure in the OpenFlight storage account run:
```shell
az storage blob list --account-name openflightimages --container-name images --query "[].{Image:name}" --output table
```

One of the images above can be imported to the desired location in your account. The following example imports the image `CENTOS7-OPENFLIGHT-2022.1-2601221729_azure.vhd` into the resource group `myimages`:
```shell
az image create --resource-group myimages --name CENTOS7-OPENFLIGHT-2022.1-2601221729 --os-type Linux --source https://openflightimages.blob.core.windows.net/images/CENTOS7-OPENFLIGHT-2022.1-2601221729_azure.vhd
```

!!!info Image Locaton
The image is hosted in the `uksouth` location. After importing as above it can be copied to other regions (`westeurope` in this example) with the image-copy extension as follows:
```shell
az image copy --source-resource-group myimages --source-object-name CENTOS7-OPENFLIGHT-2022.1-2601221729 --target-location westeurope --target-resource-group myimages --cleanup
```
!!!

===

## Configure Deployment Script

- Set the variables in `deploy.sh`:
  - `CLUSTERNAME`: The name to be assigned to the cluster
  - `LOCATION`: The region in which to deploy the cluster
  - `PUBSSHKEY`: The SSH public key for the default user (`flight`) and the `root` user to accept
  - `IMAGEPATH`: The Azure resource path to an image in `LOCATION` to be used by all instances
  - `NODECOUNT`: A number between 1-9 for the number of compute nodes to deploy

## Deploy Platform

- Run deployment script
  ```shell
  bash deploy.sh
  ```

+++
