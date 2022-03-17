---
order: 100
icon: info
---

## Overview

The Cluster Skeleton is a simple configuration of networks and compute nodes in order to test various stacks. This is used by OpenFlight as the base for the stacks tested within this section.

A Cluster Skeleton consists of:
- A head node
    - Provides a gateway to compute nodes to reach the external network
    - Shares network storage with the compute nodes over NFS
- Some compute nodes

Further information of the various skeleton deployment methods can be found below.

## Azure

### Prepare Controller

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

### Configure Deployment Script

- Set the variables in `deploy.sh`:
  - `CLUSTERNAME`: The name to be assigned to the cluster
  - `LOCATION`: The region in which to deploy the cluster
  - `PUBSSHKEY`: The SSH public key for the default user (`flight`) and the `root` user to accept
  - `IMAGEPATH`: The Azure resource path to an image in `LOCATION` to be used by all instances
  - `NODECOUNT`: A number between 1-9 for the number of compute nodes to deploy

### Deploy Platform

- Run deployment script
  ```shell
  bash deploy.sh
  ```

The above script will deploy the network, headnode and compute node templates to Azure and configure IP forwarding & firewalls on the systems.
