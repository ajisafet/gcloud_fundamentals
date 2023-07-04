Google Cloud DNS
Private zones are used to provide a namespace that is visible only inside the VPC or hybrid network environment.

For example, an organization would use a private zone for a domain dev.gcp.example.com, which is reachable only from within the company intranet. 

Public zones are used to provide authoritative DNS resolution to clients on the public internet. For example, a business would use a public zone for its external website, cymbal.com, which is accessible directly from the internet. Don’t confuse the concept of a public zone with Google Public DNS (8.8.8.8). Google Public DNS is just a public recursive resolver. 

#### Cloud DNS Policies
Cloud DNS policies provide a flexible way to define how your organization uses DNS.

#### Supported Cloud DNS Policies
 * Server policies apply private DNS configuration to a VPC network.

 * Response policies enable you to modify the behavior of the DNS resolver by using rules that you define.

 * Routing policies steer traffic based on geolocation or round robin.

Server policies are used to set up hybrid deployments for DNS resolution. You can set up an inbound server policy depending on the direction of the DNS resolutions. If your workloads plan to use an on-premises DNS resolver, you can set up DNS forwarding zones by using an outbound server policy.
If you want your on-premises workloads to resolve names on Google Cloud, you can set up an inbound server policy.

**Important: You can configure one DNS server policy for each Virtual Private Cloud (VPC) network. The policy can specify inbound DNS forwarding, outbound DNS forwarding, or both.**

In this section, inbound server policy refers to a policy that permits inbound DNS forwarding. Outbound server policy refers to one possible method for implementing outbound DNS forwarding. If a policy implements the features of both, it can be an inbound server policy and an outbound server policy. DNS server policies are not available for legacy networks. DNS server policies require VPC networks. For detailed information about server policies, see Server policies overview in the Google Cloud documentation.
To configure and apply DNS server policies, see Apply Cloud DNS server policies in the Google Cloud documentation.

A response policy is a Cloud DNS private zone concept that contains rules instead of records. These rules can be used to achieve effects similar to the DNS response policy zone (RPZ) draft concept. In other words, you can use response policies to create a DNS firewall by returning modified DNS results to clients. For example, you can use response policies to block access to specified HTTP servers.
The response policy feature lets you introduce customized rules in DNS servers within your network that the DNS resolver consults during lookups.

If a rule in the response policy affects the incoming query, it's processed. Otherwise, the lookup proceeds normally. For more information, see Manage response policies and rules in the Google Cloud documentation.

A response policy is different from an RPZ (response policy zone). An RPZ is an otherwise normal DNS zone with specially formatted data that causes compatible resolvers to do special things. Response policies are not DNS zones and are managed separately in the API.


DNS routing policies let you steer your traffic based on specific criteria. Google Cloud supports two types of DNS routing policies:
* weighted round robin
* geolocation

A weighted round robin routing policy lets you specify different weights per DNS target, and Cloud DNS ensures that your traffic is distributed according to the weights.
You can use this policy to support manual active-active or active-passive configurations. You can also split traffic between production and experimental versions of software. A geolocation routing policy lets you map traffic originating from source geographies (Google Cloud regions) to specific DNS targets. Use this policy to distribute incoming requests to different service instances based on the traffic's origin.

You can use this feature with the internet, with external traffic, or with traffic originating within Google Cloud and bound for internal load balancers. Google Cloud uses the region where queries enter Google Cloud as the source geography. An example is shown on the screen; routing policies use geolocation to route requests to the closest load balancer.

To create, edit, or delete DNS routing policies, see Manage DNS routing policies in the Google Cloud documentation.
When configuring routing policies, consider these caveats: Only one type of routing policy can be applied to a resource record set at a time.
Nesting or otherwise combining routing policies is not supported.

#### Gcloud commands from DNS Labs
Enable Cloud DNS
gcloud services enable dns.googleapis.com

Enable Compute Engine API
gcloud services enable compute.googleapis.com

gcloud services list | grep -E 'compute|dns'

*To be able to SSH into the client VMs, run the following to create a firewall rule to allow SSH traffic from Identity Aware Proxies (IAP)*
gcloud compute firewall-rules create fw-default-iapproxy \
--direction=INGRESS \
--priority=1000 \
--network=default \
--action=ALLOW \
--rules=tcp:22,icmp \
--source-ranges=35.235.240.0/20

*To allow HTTP traffic on the web servers, each web server will have a "http-server" tag associated with it. You will use this tag to apply the firewall rule only to your web servers*
gcloud compute firewall-rules create allow-http-traffic --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

*Create client VMs in us-east1 zone*
gcloud compute instances create us-client-vm --machine-type=e2-micro --zone us-east1-b

*Create client VMs in europe-west2 zone*
gcloud compute instances create europe-client-vm --machine-type=e2-micro --zone europe-west2-a

*Launch server in us-east1 region using a startup script*
gcloud compute instances create us-web-vm \
--machine-type=e2-micro \
--zone=us-east1-b \
--network=default \
--subnet=default \
--tags=http-server \
--metadata=startup-script='#! /bin/bash
 apt-get update
 apt-get install apache2 -y
 echo "Page served from: US-EAST1" | \
 tee /var/www/html/index.html
 systemctl restart apache2'

* Save the internal IP address of the web servers which is needed to set up routing policies*
export US_WEB_IP=$(gcloud compute instances describe us-web-vm --zone=us-east1-b --format="value(networkInterfaces.networkIP)")

Now that your client and server VMs are running, it's time to configure the DNS settings. Before creating the A records for the web servers, you need to create the Cloud DNS Private Zone.

*Now that your client and server VMs are running, it's time to configure the DNS settings. Before creating the A records for the web servers, you need to create the Cloud DNS Private Zone.*
gcloud dns managed-zones create example --description=test --dns-name=example.com --networks=default --visibility=private

*Create Cloud DNS Routing Policy*
In this section, configure the Cloud DNS Geolocation Routing Policy. You will create a record set in the example.com zone that you created in the previous section.
Use the gcloud dns record-sets create command to create the geo.example.com recordset:

gcloud dns record-sets create geo.example.com \
--ttl=5 --type=A --zone=example \
--routing-policy-type=GEO \
--routing-policy-data="us-east1=$US_WEB_IP;europe-west2=$EUROPE_WEB_IP"

You are creating an A record with a Time to Live (TTL) of 5 seconds. The policy type is GEO, and the routing_policy_data field accepts a semicolon-delimited list of the format ${region}:${rrdata},${rrdata}.

gcloud dns record-sets list --zone=example


gcloud compute ssh us-client-vm --zone us-east1-b --tunnel-through-iap

for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done
