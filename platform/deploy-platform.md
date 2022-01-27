---
order: 100
---
# Deploy Platform

## Overview

This documentation explains the initial configuration required for a platform in order to begin deployment of a cluster skeleton.

## How To

+++ Hardware (Omnia)

## Prepare the hardware

Prepare your hardware as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html).

## Prepare Controller

- Disable Selinux
- Install the required dependancies `python3` and `ansible` as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html)
- Clone the OpenFlight fork of Omnia
  ```shell
  git clone git@github.com:openflighthpc/omnia.git
  cd omnia
  git checkout release-1.1.1
  ```

## Configure the deployment
- Configure `omnia_config.yml` as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html)
- Create `mapping_host_file.csv` and `mapping_device_file.csv` as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html)
- Configure `control_plane/input_params/base_vars.yml` as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html)
- Set the required variables in `control_plane/input_params/login_vars.yml`


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
az image create --resource-group myimages --name CENTOS7-OPENFLIGHT-2022.1-2601221729 --os-type Linux --source https://myclustercontent.blob.core.windows.net/images/CENTOS7-OPENFLIGHT-2022.1-2601221729_azure.vhd
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
