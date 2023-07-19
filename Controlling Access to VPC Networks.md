policy constraints let you centrally manage access to Google Cloud resources, including VPC networks.

Identity and Access Management focuses on who, and lets the administrator authorize who can take action on specific resources based on permissions. Organization Policy 
focuses on what, and lets the administrator set restrictions on specific resources to determine how they can be configured.
The Organization Policy Service gives you centralized and programmatic control over your organization's cloud resources. As the organization policy administrator, you will be able to configure constraints across your entire resource hierarchy.

An organization policy is a configuration of restrictions. You, as the organization policy administrator, define an organization policy, and you set that organization policy on organizations, folders, and projects in order to enforce the restrictions on that resource and its descendants. In order to define an organization policy, you choose a constraint, which is a particular type of restriction against either a Google Cloud service or a group of Google Cloud services.

You configure that constraint with your desired restrictions. Descendants of the targeted resource hierarchy node inherit the organization policy. By applying an organization policy to the root organization node, you are able to effectively drive enforcement of that organization policy and configuration of restrictions across your organization.
