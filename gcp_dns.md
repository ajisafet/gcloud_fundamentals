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

**You can configure one DNS server policy for each Virtual Private Cloud (VPC) network.**




