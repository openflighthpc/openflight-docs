# OpenFlight

## Description

OpenFlight provides simple platform deployment methods for multiple cloud and virtualisation platforms to get a basic Cluster Skeleton running in minutes.

## Quick Review

|    Category     |                                       Rating                                         |
| :-------------: | :----------------------------------------------------------------------------------: |
| Ease of Use     | :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star:      |
| Customisability | :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star: :icon-star:           | 
| Adding Nodes    | :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star: :icon-star:           |
| Debuggability   | :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star-fill: :icon-star:      |


- **Ease of Use:** How intuitive the solution is to obtain, configure & launch
- **Customisability:** The extent to, and simplicity of, which the solution can be modified to suit a desired deployment
- **Adding Nodes:** How achievable it is to add nodes to a live/existing system with the solution
- **Debuggability:** How possible it is to identify reasons for deployment failure

### Ease of Use

In order to minimise complications, the OpenFlight deployment methods avoid abstraction as much as possible. Simply using BASH scripts and templates to deploy the desired resources.

### Customisability

Due to the use of templates, those familiar with the platform they are deploying to will be able to modify platform-native templates as they see fit.

### Adding Nodes

With the simple use of templating and static network configuration, the addition of extra nodes can be through similar methods to the base deployment.

### Debuggability

Using platform-native tools and scripts reduces the redirection and obscuration of error messages so identifying where issues in the deployment process arise can be quickly determined.

## Detailed Review

The detailed review provides a breakdown of the OpenFlight experience in using this solution.

### Platform Requirements

Due to the various platform deployment methods, any platform-specific setup is detailed in the relevant tab. 

### Our Setup Experience

+++ Azure

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

+++ AWS

!!!danger
Coming soon...
!!!

+++ Libvirt/KVM

!!!danger
Coming soon...
!!!

+++ OpenStack

!!!danger
Coming soon...
!!!

+++

