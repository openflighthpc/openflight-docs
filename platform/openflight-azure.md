# Overview

An example _Cluster Skeleton_ on Azure, deployed using a bash script and some templates.

# Deployment

## Prepare Deployment Server

- [Install Azure CLI]()
- [Authenticate Azure CLI]()
- Clone the OpenFlight Platform Examples
  ```shell
  git clone https://github.com/openflighthpc/platforms-examples
  ```
- Navigate into the repo and Azure directory
  ```shell
  cd platforms-example/azure
  ```

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
