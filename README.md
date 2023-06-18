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


Dynamic Routes
Dynamic routes are managed by Cloud Routers in the VPC network. Their destinations always represent IP address ranges outside your VPC network, which are received from a BGP peer router.
BGP peer routers are typically outside the Google network (like on-premises or another cloud provider). Dynamic routes are used by: 
1. Dedicated Interconnect
2. Partner Interconnect
3. HA VPN tunnels
4. Classic VPN tunnels that use dynamic routing

Routes are added and removed automatically by Cloud Routers in your VPC network. The routes apply to VMs according to the VPC network's dynamic routing mode. This example shows a VPC network connected to an on-premises network that uses Dedicated Interconnect. Cloud Router handles the BGP advertisements and adds them as custom routes. Cloud Router creates a BGP session for the VLAN attachment and its corresponding on-premises peer router. The Cloud Router receives the routes that your on-premises router advertises.
These routes are added as custom dynamic routes in your VPC network. The Cloud Router also advertises routes for Google Cloud resources to the on-premises peer router.



In your VPC networks, you are not limited to IP addresses that are assigned by Google Cloud. Next, let’s discuss BYOIP, or Bring Your Own IP addresses into Google Cloud.
00:13
BYOIP enables customers to assign IP addresses from a public IP range that they own to Google Cloud resources. With BYOIP, customers can route traffic directly from the internet to their VMs without having
00:27
to go through their own physical networks. After the IP addresses are imported, Google Cloud manages them in the same way as Google-provided IP addresses, with these exceptions: The IP addresses are available only to the customer who brought them.
00:42
Idle or in-use IP addresses incur no charges. The object that the IP address is assigned to can have a regional scope, like a VM or the forwarding rule of a network load balancer.
00:55
It can also have a global scope, like the forwarding rule of a global external HTTP(S) load balancer. It must support an external address type, because BYOIP ranges will be advertised by
01:06
Google to the public internet. A BYOIP address can’t be assigned to a Classic VPN gateway, GKE (Google Kubernetes Engine) node, GKE pod, or an autoscaling MIG (managed instance group).

BYOIP Caveats
BYOIP prefixes cannot overlap with subnet or alias ranges in the VPC used by the customer. For BYOIP, the IP address must be IPv4. Importing IPv6 addresses is not supported.
01:38
Overlapping BGP route announcements can be problematic. BGP is a routing protocol that picks the most efficient route to send a packet. If Google and another network advertise the same route with matching or mismatched prefix
01:52
lengths, BGP cannot work properly. You might experience unexpected routing and packet loss. For example: suppose you're advertising a 203.0.112.0/20 address block and you're using BGP to route packets. You could bring a 203.0.112.0/23 address block that you own to Google using BYOIP, and set
02:18
it up to route externally. Because the /23 block is contained within the /20 block, BGP route announcements can/might overlap. If you're maintaining the routing registry correctly, BPG routing practices cause the
02:32
more specific route to take precedence. Thus, the /23 block will take precedence over the /20 block. However, if the /23 route ever stopped being advertised, the /20 block could be used.

Multiple Network Interfaces
In conventional networking, devices can use multiple network interfaces to communicate with multiple networks. Next, let’s discuss using multiple network interfaces in a Google Cloud VPC network. VPC networks are isolated private networking domains by default.
00:19
As we mentioned earlier, VM instances within a VPC network can communicate among themselves by using internal IP addresses as long as firewall rules permit. However, no internal IP address communication is allowed between networks unless you set
00:35
up mechanisms such as VPC peering or VPN. Every instance in a VPC network has a default network interface. You can create additional network interfaces attached to your VMs through network interface
00:49
controllers (NICs). Multiple network interfaces let you create configurations in which an instance connects directly to several VPC networks. Each of the interfaces must have an internal IP address, and each interface can also have
01:03
an external IP address. For example, in this diagram, you have two VM instances. Each instance has network interfaces to a subnet within VPC1, VPC2, and VPC3. For some situations, you might require multiple interfaces; for example, to configure an instance
01:24
as a network appliance for load balancing. Multiple network interfaces are also useful when applications running in an instance require traffic separation, such as separation of data plane traffic from management plane traffic.
01:39
When creating VM instances with multiple network interfaces, note these caveats. You can only configure a network interface when you create an instance. Each network interface configured in a single instance must be attached to a different VPC
01:54
network. Each interface must belong to a subnet whose IP range does not overlap with the subnets of any other interfaces. The additional VPC networks that the multiple interfaces will attach to must exist before
02:08
you create the instance. You cannot delete a network interface without deleting the instance. When an internal DNS (Domain Name System) query is made with the instance hostname, it resolves to the primary interface (nic0) of the instance.
02:26
If the nic0 interface of the instance belongs to a different VPC network than the instance that issues the internal DNS query, the query will fail. You will explore this in the upcoming lab.
02:38
The maximum number of network interfaces per instance is 8, but this depends on the instance's machine type, as shown in this table: Instances with less than or equal to 2 vCPU can have up to 2 virtual NICs.
02:54
Examples include the f1-micro, g1-small, n1-standard-1, and any other custom VMs with 1 or 2 vCPUs. Instances with more than 2 vCPU can have 1 NIC per vCPU, with a maximum of 8 virtual
03:12
NICs.

# Create firewall rule
gcloud compute --project=qwiklabs-gcp-00-895ef3240821 firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0

# Subnets span a region, VPCs spans multiple regions
# This is unlike AWS where a VPC spans a region and subnets span a single availability zone

command to create the privatenet network:
gcloud compute networks create privatenet --subnet-mode=custom

command to create the privatesubnet-us subnet
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24

command to create the privatesubnet-eusubnet
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west3 --range=172.20.0.0/20

command to list the available VPC networks:
gcloud compute networks list

As expected, the default and mynetwork networks have subnets in each region, because they are auto mode networks. The managementnet and privatenet networks only have the subnets that you created, because they are custom mode networks.

# Create firewall rule
gcloud compute --project=qwiklabs-gcp-00-895ef3240821 firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0

# List firewall rules
gcloud compute firewall-rules list --sort-by=NETWORK
