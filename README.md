# gcloud_fundamentals

IPv6
VPC networks now support IPv6 addresses
Support for IPv6 addresses can vary per subnet
To support IPv6, Google Cloud has introduced the concept of a subnet stack
1. Single-stack subnet supports IPv4
2. Dual-stack subnet supports IPv4 and IPv6

You can configure IPv6 access type as internal or external

Internal IPv6 addresses are used for communication between VMs within VPC networks. These use unique local address and can't be routed over the internet

External IPv6 addresses
1. Can be used for communication between VMs within VPC networks. These use global unicast addresses (GUAs)
2. Are also routable on the internet

Connected VMs inherit IPv6 access type from subnet

To enable internal IPv6 on a subnet, you must first assign an internal IPv6 range on the VPC network

A //48 ULA range from within fd20::/20 is assigned to the network
1. All internal IPv6 subnet ranges in the network are assigned from thus /48 range
2. The /48 range can be automatically assigned or you can select a specific range from within fd20::/20

When you enable IPv6 on a VM, the VM is assigned a /96 range from the subnet. The first IP address in that range is assigned to the primary interface
You don't have to configure whether a VM gets internal or external IPv6 addresses. The VM inherits the IPv6 access type from the subnet its connected to

IPv6 Caveats
1. Dual stack subnets are not supported on auto mode VPC networks or legacy networks. If you have auto mode VPC network you want to add dual stack subnets to (support IPv6 addresses), you can convert it to a custom network
2. If you're converting a legacy custom network, create new dual-stack subnets, or convert existing subnets to dual-stack.
3. Any interface on a VM can have IPv6 addresses configured.
