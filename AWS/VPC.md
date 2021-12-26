### VPC

VPC has its range of IPv4 addresses, e.g. `10.0.0.0/16`

A VPC is a regional network that can include subnets in different AZs in the region.\
After you create a VPC, you can add one or more subnets in each Availability Zone.\
VPC can not span across multiple regions.

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
