---
order: 98
---

# DNS

There are multiple options for DNS for the internal cluster subnet, such as:
- Local DNS
- NIS
- IPA

Some further information and guidance on these processes can be found below. 

## Example Configuration (CentOS 7 & Rocky 8)

+++ Local DNS

Local DNS involves manually configuring `/etc/hosts` on all nodes to contain the IP addresses, short hostname and long hostname of all nodes in the cluster. 

An example entry for `/etc/hosts` for the head node with an IP of `10.10.1.1` and the cluster cluster domain `pri.mycluster.network`:
```shell
10.10.1.1    head1 head1.pri.mycluster.network
```

+++ NIS


+++ IPA

+++
