# Overview

An example _Cluster Skeleton_ deployed using the Dell Omnia deployment method.

# Deployment

## Prepare the hardware

Prepare your hardware as per the [Omnia Documentation](https://dellhpc.github.io/omnia/INSTALL_OMNIA_CONTROL_PLANE.html).

## Prepare Deployment Server

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
