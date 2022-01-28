---
order: 99
---

# IP Forwarding & Masquerade 

IP forwarding will allow the computational nodes to reach the external network and the Internet to install additional packages.

## Example Configuration (CentOS 7 & Rocky 8)

A common way to configure IP forwarding on the head node is to:
- Add the internal interface to the trusted firewall zone
  ```shell
  firewall-cmd --add-interface INTERNAL_INTERFACE --zone trusted --permanent
  ```
- Add the external interface to the public firewall zone
  ```shell
  firewall-cmd --add-interface EXTERNAL_INTERFACE --zone public --permanent
  ```
- Configure masquerade for the internal subnet on the public firewall zone
  ```shell
  firewall-cmd --add-rich-rule='rule family="ipv4" source address="CLUSTER_SUBNET/NETMASK" masquerade' --zone public --permanent
  ```
- Make the firewall changes live
  ```shell
  firewall-cmd --reload
  ```
- Enable IP forwarding
  ```shell
  echo 'net.ipv4.ip_forward = 1' > /etc/sysctl.conf
  echo 1 > /proc/sys/net/ipv4/ip_forward
  ```

Compute nodes can be set to use the head node as a network gateway via either platform mechanisms (e.g. switch configurations or virtual network routing) or through the nodes themselves as follows:
- Set default route via the head node IP through the internal network interface of the node
  ```shell
  route add default gw HEAD_IP INTERNAL_INTERFACE
  ```
- Add the default gateway to the internal network interface configuration file
  ```shell
  GATEWAY=HEAD_IP
  ```
