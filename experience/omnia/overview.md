---
order: 99
---

# Overview

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

