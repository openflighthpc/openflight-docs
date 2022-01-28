---
order: 99
---
# Configure Cluster Services

## Prerequisites

To setup cluster services, you will need to have the following information for your platform:
- `domain_name`: The FQDN for the internal subnet
- `head_ip`: The IP address of the head node
- `storage_ip`: The IP address of the storage node (note: in a simple setup, this can also be the head node)
- `login_ip`: The IP address of the login node (note: in a simple setup, this can also be the head node)
- `compute_ip_range`: The IP address range for the internal subnet

It is expected that your platform at this point has:
- OSes installed on all systems
- Internal network IPs assigned and configured on all systems
- External IP address obtained on the head node
- Passwordless SSH from the `root` user on the controller to all nodes on the system

## Deploy Cluster Services

Below are some examples and guidelines for cluster service configuration.

### Networking

[!ref :icon-flame: Firewalls](cluster-services/firewall.md)

[!ref :icon-arrow-switch: IP Forwarding & Masquerade](cluster-services/ip-forwarding-and-masquerade.md)

[!ref :icon-globe: DNS](cluster-services/dns.md)

