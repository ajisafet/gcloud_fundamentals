# gcloud_fundamentals

command line access
gcloud
gsutil
bq

API Access
https://developers.google.com/apis-explorer
https://cloud.google.com/apis/docs/cloud-client-libraries

Cloud Shell

API
https://cloud.google.com/apis/docs/cloud-client-libraries

Google Cloud Mobile



Compute Engine pricing and billing offers
Sustained-use - Usage for up to a month 
Committed-use - Sign up to a long term contract get 15-20% discount
Pre-emptible and spot VMs - 30 secs for graceful shutdown. Preemt[tible has a max time of 24 houyrs before shutdown. Spot has no limits

Firewall rules and policy
Firewall policy allows you to assign rules to multiple resources

 a route is apolicy that governes the destination to which a network packet is sent
 
 
 VPC peering and sharing allow projects to communicate
 VPC peering allows traffic between separate VPCs.
 
 Cloud DNS
 Google provides public DNS service - 8.8.8.8 is based on Cloud DNS
 
 Subnets have regional scope in GCP
 
 
 
VPCs
Google Cloud Virtual Private Cloud (VPC) provides networking functionality to Compute Engine virtual machine (VM) instances, Kubernetes Engine containers, and App Engine flexible environment. In other words, without a VPC network you cannot create VM instances, containers, or App Engine applications. Therefore, each Google Cloud project has a default network to get you started.

You can think of a VPC network as similar to a physical network, except that it is virtualized within Google Cloud. A VPC network is a global resource that consists of a list of regional virtual subnetworks (subnets) in data centers, all connected by a global wide area network (WAN). VPC networks are logically isolated from each other in Google Cloud.

In this lab, you create an auto mode VPC network with firewall rules and two VM instances. Then, you explore the connectivity for the VM instances.

Every VPC network has two implied firewall rules that block all incoming connections and allow all outgoing connections.

Summary
In this lab, you explored the default network along with its subnets, routes, and firewall rules. You deleted the default network and determined that you cannot create any VM instances without a VPC network.

Thus, you created a new auto mode VPC network with subnets, routes, firewall rules, and two VM instances. Then you tested the connectivity for the VM instances and explored the effects of the firewall rules on connectivity.


Cloud Storage
Object Storage

Cloud SQL
Fulluy managed SQL database. Supports PostgresSQL, MySQL and. For OLTP and linited capacity of 64TB

Cloud Spanner
Fulluy managed SQL database which scales horizontally. For OLTP and but higher capacity thank Cloud SQL (petabytes)

Firestore
Fully managed NoSQL database which scales horizontally.

Cloud Bigtable
Google's NoSQL big data service built for analytics workloads

BigQuery
It is an enterprise data warehouse


Containers In The CLoud notes



App Engine
Fully managed application. Pay for resources used. Camn also inform ypu of vulns in your conde

Requirements
Use specific versions of JKabva, Pyhton, Ruby, PHP, node
Your application must conform to sandbox constraints that are dependent on runtime.


Cloud Run

    8  echo $LOCATION 
    9  mkdir helloworld && cd helloworld
   10  nano package.json
   11  ls
   12  cat package.json 
   13  nano index.js
   14  cat index.js 
   15  nano Dockerfile
   16  env | greop GOOGLE
   17  env | grep GOOGLE
   18  gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld
   19  gcloud container images list
   20  docker run -d -p 8080:8080 gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld
   21  curl localhost:8080
   22  curl localhost:8080; echo
   23  gcloud run deploy --image gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld --allow-unauthenticated --region=$LOCATION
   24  gcloud container images delete gcr.io/$GOOGLE_CLOUD_PROJECT/helloworld
   25  gcloud run services delete helloworld --region=us-central1
   26  history
   

Internal IP - 10.128.0.2
External IP - 34.135.214.96


34.132.147.99 - MySQL public IP

Cloud SQL
Authorized networks
You can specify CIDR ranges to allow IP addresses in those ranges to access your instance. Learn more
https://cloud.google.com/sql/docs/mysql/authorize-networks?_ga=2.226119455.-408308009.1683627345
