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


Routes and Route Types
Defines the path that network traffic takes from a VM to other destinations. These destinations can be within a VPC or outside it.

Routes match traffic by destination IP address however no traffic will flow wiuthout a matching firewall rule. A route is created when network is created which enables traffic delivery from anywhere.

A route is also created when a subnet is created. This allows VMs on the same sub-network to communicate.

Netwirk tags finetune which route is picked. If a route has a network tag, it can be applied only to instances that have the same network tag

Routes without tags can apply to all instances in the network.

A route is created when a network or subnet is created, enabling traffic delivery from anywhere.

Route Types
Routes can be:
* System-generated routes are simple can be used by default. When they don't provide the desired granularity use custom routes
* Custom routes can be used to route trafific between subnets through network virtual appliance
* Peering are used for network peering


System-generated routed
When you create a VPC network, it includes a system-generated IPv4 default route (0.0.0.0/0).

When you create a dual-stack subnet with an external IPv6 address range in a VPC network, a system-generated IPv6 default route (::/0) is added.

If the default route doesn't exist, it isn’t added.
The IPv4 and IPv6 default routes that serve these purposes define a path out of the VPC network to external IP addresses on the internet.

If you access Google APIs and services without using a Private Service Connect endpoint, the default route can serve as the path to Google APIs and services.

Private Service Connect enables you to publish and consume services by using the internal IP addresses that you define.

You’ll learn more about Private Service Connect later in this course. For more information, in the Google Cloud documentation, refer to Configuring Private Google Access and Accessing APIs from VMs with external IP addresses.

Google Cloud only uses a default route if a route with a more specific destination does not apply to a packet. For information about how destination specificity and route priority influence route selection, see Routing order in the Google Cloud documentation.

To completely isolate your network from the internet or to replace the default route with a custom route, you can delete the default route.

For IPv4 only, to route internet traffic to a different next hop, you can replace the default route with a custom static or dynamic route. For example, you could replace it with a custom static route whose next hop is a proxy VM.

If you delete the default route and do not replace it, packets to IP ranges not covered by other routes are dropped. If you don't have custom static routes that meet the routing requirements for Private Google Access, deleting the default route might disable Private Google Access.

Some organizations do not want a default route pointing to the internet; instead, they want the default route to point to an on-premises network. To do that, you can create a custom route.

When you create a subnet, system-generated subnet routes are automatically created. Subnet routes apply to the subnet, not the whole network. They always have the most specific destination and cannot be overridden by higher priority routes. Recall that lower priority number indicate higher priority, so 1 would have a higher priority than 10. Each subnet has at least one subnet route whose destination matches the primary IP range of the subnet. If the subnet has secondary IP ranges, each secondary IP address range has a corresponding subnet route.

Overview of Custom Static Routes
Custom static routes forward packets to a static route next hop and are useful for small, stable topologies. Dynamic routing generally provides quicker routing performance.

Benefits of custom static routing
1. Unlike dynamic routing, no processing power is devoted to maintaining and modifying the routes (hence the quicker performance)
2. Custom static routing is more secure than dynamic routing, because there’s no route advertisement.

Limitations
1. A custom static route cannot point to a VLAN attachment.
2. It also requires more maintenance, because routes are not dynamically updated. i.e. For example, a topology change on either network requires you to update static routes. Also, if a link fails, static routes can’t reroute traffic automatically. For small, stable topologies, this is not always a significant concern.

Route changes are propagated to the VM controllers. When you add or delete a route, the set of changes is propagated to the VM controllers.
You can create custom static routes either manually or automatically.
To create custom static routes manually, use the Google Cloud Console, the gcloud CLI compute routes create command, or the routes.insert API.
To create the routes automatically, you can use the Google Cloud app to create a Classic VPN tunnel with policy-based routing or as a route-based VPN.
For more information, see Cloud VPN networks and tunnel routing.

