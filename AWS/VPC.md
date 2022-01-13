### VPC

VPC has its range of IPv4 addresses, e.g. `10.0.0.0/16`

A VPC is a regional network that can include subnets in different AZs in the region.\
After you create a VPC, you can add one or more subnets in each Availability Zone.\
VPC can not span across multiple regions.

Max CIDR per VPC is 5, for each CIDR:
* min size is /28 (16 IP addresses)
* max size is /16 (65536 IP addresses)

:exclamation: VPC CIDR should not overlap with your other Networks (e.g., corporate)\
:exclamation: 5 addresses in each CIDR/subnet are always reserved by AWS. Hence, `/27` gives not 32 IP addresses, but 32-5 = 27

A subnet is a range of IP addresses in your VPC.
Subnet types:
* Public subnet: The subnet's IPv4 or IPv6 traffic is routed to an internet gateway and can reach the public internet.
* Private subnet: The subnet’s IPv4 or IPv6 traffic is not routed to an internet gateway and cannot reach the public internet.
* VPN-only subnet: The subnet doesn't have a route to the internet gateway, but it has its traffic routed to a virtual private gateway for a Site-to-Site VPN connection


![subnets-diagram](files/subnets-diagram.png)

Note the following:

* 1A, 2A, and 3A represent instances in your VPC.
* Subnet 1 is a public subnet, subnet 2 is a private subnet, and subnet 3 is a VPN-only subnet.
* An internet gateway enables communication over the internet, and a virtual private network (VPN) connection enables communication with your corporate network.


Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet.

The route table associated with subnet 1 routes all IPv4 traffic (`0.0.0.0/0`) and IPv6 traffic (`::/0`) to an internet gateway (for example, `igw-1a2b3c4d`).\
Because instance 1A has an IPv4 Elastic IP address and an IPv6 address, it can be reached from the internet over both IPv4 and IPv6.

The instance 2A can't reach the internet, but can reach other instances in the VPC.\
You can allow an instance in your VPC to initiate outbound connections to the internet
over IPv4 but prevent unsolicited inbound connections from the internet using a network
address translation (NAT) gateway or a NAT instance.

The route table associated with subnet 3 routes all IPv4 traffic (`0.0.0.0/0`) to a virtual private gateway (for example, `vgw-1a2b3c4d`).\
Instance 3A can reach computers in the corporate network over the Site-to-Site VPN connection.

#### IGW Internet Gateway
Enables resources **with public IPs** in VPC connect to the Internet.
* Must be created separately from VPC
* One VPC can only be attached to one IGW and vice versa
* IGW on its own does not allow Internet access - Route Tables associate with subnets also must be edited!

#### NAT Gateway
A NAT gateway is a Network Address Translation (NAT) service.\
You can use a NATGW so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances.

* When you create a NATGW, you specify one of the following connectivity types:
  * **Public** – (Default) Instances in private subnets can connect to the internet through a public NAT gateway, but cannot receive unsolicited inbound connections from the internet.
  You create a public NAT gateway in a **public subnet** and must associate an **elastic IP** address with the NAT gateway at creation.
  * **Private** – Instances in private subnets can connect to other VPCs or your on-premises network through a private NAT gateway. 
* AWS Managed (unlike NAT Instance). No administration. Supersedes NAT Instances.
* Paid per hour of usage
* NATGW is created in a specific AZ
* Can't be used by EC2 instances in the same subnet (only from other subnets)
* Requires an IGW (Private Subnet -> NATGW -> IGW)
* Bandwidth is 5 Gbps with auto scaling up to 45 Gbps
* No need to manages SGs! (unlike NAT Instances)

![](files/NAT_Gateway.png)

NATGW is resilient within a single AZ only.\
To achieve fault tolerance and hi availability it is necessary to create multiple NATGWs in multiple AZs\
![](files/Multiple_NATGW.png)

#### NAT Instance
NAT = Network Address Translation
Allows EC2 in private subnet ot connect to the Internet.\
:exclamation: NAT Instances are superseded by the NAT Gateway!
* Must be launched in a public subnet
* Must disable EC2 setting: `Source destication check`
* Must have **Elastic IP** attached
* Must manage SGs on NAT Instance
* Need to use special pre-configured AMIs
* Can be used as a Bastion host

![](files/NAT_instance.png)

NAT Instances re-writes network packets changing source IP.

#### Bastion host
How to give access to EC2 hosts in a **private subnet**?\
There is a concept of **bastion host** - an EC2 host placed in a public subnet and open to the Internet.\
Users can ssh into bastion host first then ssh from bastion host to target hosts in the private subnet (2 hops).

To do list:
* Instances in private subnet must start with an allowed ssh keypair.
* Instances in private subnet must allow the SG of the bastion host in their SG.
* After connecting to the Bastion host it is necessary to import an ssh key to it (can be tricky if copied as text, mind the new line in the end)

#### VPC Sharing
Use VPC sharing to share **one or more subnets** with other AWS accounts belonging\
to the same parent organization from AWS Organizations.\
The owner account **cannot share the VPC itself**.

VPC sharing (part of Resource Access Manager) allows multiple AWS accounts to create their\
application resources such as EC2 instances, RDS databases, Redshift clusters, and Lambda functions,\
into shared and centrally-managed Amazon Virtual Private Clouds (VPCs).\
To set this up, the account that owns the VPC (owner) shares one or more subnets with\
other accounts (participants) that belong to the same organization from AWS Organizations.

After a subnet is shared, the participants can view, create, modify, and delete their\
application resources in the subnets shared with them.\
Participants cannot view, modify, or delete resources that belong to other participants or the VPC owner.

#### VPC Peering
A VPC peering connection is a networking connection between two VPCs that enables you\
to route traffic between them using private IPv4 addresses or IPv6 addresses.\
Instances in either VPC can communicate with each other as if they are within the same network.\
Unlike VPC sharing VPC peering does not facilitate centrally managed VPCs.

* Can be cross account and cross regions
* Must not have overlapping CIDRs
* VPC Peering is not **transitive**, i.e. peering VPC_A <-> VPC_B <-> VPC_C does not make VPC_A <-> VPC_C
* Must update Route Tables in each VPC's subnets to ensure EC2s can communicate
  * Destination = another VPC CIDR
  * Target = "peering connection"

#### NACLs vs SGs
Network Access Control List (NACL) = firewall at subnet level.
* New subnets are assigned the **Default NACL**.
  * :exclamation: Default NACL allows all traffic in and all traffic out
* NACL Rules are stateless, i.e. inbound and unbound rules are evaluated independently.
  * Rules have a number (1-32766). Higher precedence with a lower number.
  * First rules match will drive the decision
  * The last rule is an asterisk and denies a request in case of no rule match
* NACL must deal with **Ephemeral ports** (short lived random ports that a client opens and holds to get a response from a server)

Use case: blocking specific IPs at the subnet level.\
![](files/NACL_SGs_incoming.png)\
![](files/NACL_SGs_outgoing.png)

![](files/SG_vs_NACL.png)

#### VPC Reachability Analyzer
A tool to check connectivity between two endpoints in VPC.\
It will check NACLs, SGs, Route tables, ENIs and so on without sending actual network packets.\
It is a paid per analysis service.

Use case: troubleshooting connectivity in case of complex network topology

#### VPC Endpoints (AWS PrivateLink)
1. How to enable hosts in private subnets to access AWS services like S3?\
It is possible to establish EC2 -> NATGW -> IGW -> S3 connection but it is not optimal because it is routed through the Internet.

Instead, a **VPC Endpoint** can be used.\
A VPC endpoint enables connections between a virtual private cloud (VPC) and supported services, without requiring that you use an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection.\
**AWS PrivateLink** is a highly available, scalable technology that enables you to privately connect your VPC to supported AWS services.

Types of VPC Endpoints:
* **Interface endpoint**. Provisions an ENI (private IP) as an entry point (must attach SG). Support most AWS services\
![](files/VPC_interface_endpoint.png)
* **Gateway endpoint**. Deprecated. Provisions a gateway and must be used as a target in a route table. Supports S3 and Dynamodb\
![](files/VPC_gateway_endpoint.png)

2. How to expose a service in a VPC to many other VPCs?\
Without making it public and without creating VPC Peerings (both options are not optimal).

To expose a service an **AWS Private Link** can be used. Both sides must:
* on the service VPC side have NLB
* on the service VPC side create an **Endpoint Service** at VPC > Endpoint Services. Attach NLB.
* on the customer VPC side have ENI (??)
* on the customer VPC side go to Endpoints and "find a service by name"

![](files/PrivateLink.png)

![](files/PrivateLink2.png)

#### VPC Transit Gateway
For transitive peering between many VPCs and on-premise DCs.\
Makes a star connection topology.

TGW is a regional resource, but can work **cross-region** and **cross-accounts**.

To limit anyone to anyone peering a **Route Table** associated with the TGW can be edited.

![](files/TGW.png)

#### Site-to-Site VPN
By default, instances that you launch into an Amazon VPC can't communicate with your own (remote) network.\
You can enable access to your remote network from your VPC by creating an AWS Site-to-Site VPN connection, and configuring routing to pass traffic through the connection.

Concepts:
* Customer gateway: An AWS resource which provides information to AWS about your customer gateway device.
  * What IP address to use? 
  * If CGW has a public IP then public IP
  * IF CFW has private IP then it should be linked to a NAT device, which should have a public IP
* Virtual private gateway: The VPN concentrator on the Amazon side of the Site-to-Site VPN connection. You use a virtual private gateway or a transit gateway as the gateway for the Amazon side of the Site-to-Site VPN connection.
  * :exclamation: enable **Route Propagation** for VPGW in the route table associated with subnets
* Transit gateway: A transit hub that can be used to interconnect your VPCs and on-premises networks. You use a transit gateway or virtual private gateway as the gateway for the Amazon side of the Site-to-Site VPN connection.

![](/home/andreychirkov/Projects/refcards/AWS/files/vpn-how-it-works-vgw.png)\
![](files/vpn-how-it-works-tgw.png) 

#### VPN CloudHub
If you have multiple AWS Site-to-Site VPN connections, you can provide secure communication between sites using the AWS **VPN CloudHub**.\
This enables your remote sites to communicate with each other, and not just with the VPC.\
The VPN CloudHub operates on a simple hub-and-spoke model that you can use with or without a VPC.\
This design is suitable if you have multiple branch offices and existing internet connections and would like to implement a convenient, potentially low-cost hub-and-spoke model for primary or backup connectivity between these remote offices.

![](files/AWS_VPN_CloudHub-diagram.png)

#### VPC Flow Logs
VPC Flow Logs capture info about IP traffic at levels:
* VPC
* Subnet
* ENI

Flow logs data can go to S3 / CloudWatch Logs.\
Flow logs data can be queried with Athena in S3 or with CloudWatch Logs Insights.

Flow logs for VPC can be created at VPC > Flow Logs pane.

Use cases: 
* troubleshooting why traffic is rejected
* security checks: from which IPs resources are accessed. This can be used later to set up WAF or NACL.

![](files/VPC_Flow_logs.png)


#### AWS Direct Connect
Provides a dedicated **private** connection (not routed ver public Internet) between your DC and VPC.\
For this a dedicated connection must be set up between client's DC and AWS Direct Connect locations.\
Client must also set up a VPGW on his VPC.\
Use cases:
* Increase bandwidth throughput
* Security: connection does not go over Internet

:exclamation: It can a long time to set up! (up to months)

![](files/DirectConnect1.png)

In case of multiple regions/VPCs it is necessary to use **DirectConnect Gateway**:\
![](files/DirectConnect2.png)

#### Traffic Mirroring
Allows you to capture and inspect network traffic in a VPC.\
Routes the traffic to security appliances.\
Captures traffic:
* From (Source) - ENIs
* To (Targets) - ENI or NLB

Can capture all packets or filter some (optionally truncate packets).\
Source and target can be in the same VC or in different(VPC Peering).\
Use cases: content inspection, threat monitoring, troubleshooting.

#### More on how to connect VPC to VPC and VPC to on-premise Network
[Amazon Virtual Private Cloud Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/introduction.html)
