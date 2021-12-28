### Route53

Route 53 supports following DNS record types
* A - maps hostname to ipv4
* AAAA - ipv6
* CNAME - maps hostname to hostname
* NS - name servers for hosted zones. A DNS name server is a server that stores the DNS records
* and some others..

Hosted zones - containers of records defining how traffic is routed to domain and its subdomains.\
`Public hosted zones` for internet, e.g. `app.company.com`.\
`Private hosted zones` for private domain names within VPC, e.g. `app.company.internal.com`.

#### CNAME vs Aliases
Both CNAME records and Route 53 "Alias" can be used to redirect to other hostname.\
Alias is Route53 specific and can refer directly to AWS resources: ELBs, API Gateway, S3 Websites, VPC endpoints etc.\
:exclamation: Alias can not refer to EC2 DNS name.\
Aliases are free of charge.

#### Routing policies:
* `Simple`. Static routing. Can respond with multiple values - in this case a client picks an address randomly from the list.
* `Weighted`. Assumes multiple resource. Controls the % of traffic going to each resource. Each record gets a `weight` assigned.
Traffic share = weight / sum (all weights). Records with `0` weight does not receive traffic.
* `Latency`. Redirects to a resource with least latency to a client. Latency is based on traffic between users and AWS regions and is evaluated by Route53.
When specifying a record with latency-based policy it is necessary to pick a region corresponding to the given target (AWS can not find out it automatically).
* `Failover`. Failover record can be of types primary and secondary. The primary one must have a healthcheck. For secondary it is optional. Once the primary record target becomes unreachable, Route53 switches to the secondary.
* `Geolocation`. Based on where a user is actually located - this is different from `Latency`. Record has a specified location - continent, country, US state. The policy also has a "default record" (for case when no match on location). Records can also have healthchecks.
* `Geoproximity`. Routes traffic based on the geo location of users and resources. There is an ability to shift more traffic to resources based on the defined `bias`. The bias value is within [-99,99] where positive values = more traffic. Resources can be AWS (with specified AWS region) or non-AWS (with specified latitude and longitude). To activate this the `Route53 Traffic flow` feature must be activated. With bias=0 always this policy works as `Geoproximity`.
* `Multivalue answer`. Used to route traffic to multiple resources. Can be associated with healthchecks. Route53 returns up to 8 healthy records. A client is able to decide which address to use (like client-side load-balancing).

#### Traffic flow
Under traffic flow a traffic flow policies can be created using GUI.\
This substitutes manual records creation.\
Traffic flow policies can be versioned.\
After creating a traffic policy policy records can be created from it.

#### Healthchecks
Route53 healthchecks can provide automatic DNS failover.\
They work only with public hosted zones.

Types of healthchecks:
1. Monitor healthchecks.\
There are 15 global AWS healthcheckers that will check the endpoint health.\
Healthchecks accepts 2xx and 3xx status codes as healthy.
If >18% checkers report healthy then Route53 consider target as healthy.\
Healthchecks must be allowed in firewall.

2. Calculated healthchecks.\
Combines results of multiple healthchecks into a single health with OR, AND, NOT operands.

3. State of CloudWatch alarm.\
For private hosted zones the workaround is to have a CloudWatch metric/alarm pair for EC2 instance and then create a healthcheck which refers to this alarm.

#### Domain registrar vs DNS service
It is possible to use 3rd party domain registrars and Route53 together.\
For this it is necessary to create a public hosted zone in Route53 and then set NS records in the domain registrar to Route53 name servers.

#### TTL
Each DNS record has a TTL (Time To Live) which orders clients for how long to cache the reply. Hence depending on the TTL value sometimes it is possible to observe a lag when updating a DNS record.

#### Testing DNS Records
`nslookup <host>`
`dig <host>`

#### Route 53 Resolver
Amazon Route 53 effectively connects user requests to infrastructure running in AWS – such as Amazon EC2 instances – and can also be used to route users to infrastructure outside of AWS.\
By default, Route 53 Resolver automatically answers DNS queries for local VPC domain names for EC2 instances.\
You can integrate DNS resolution between Resolver and DNS resolvers on your on-premises network by configuring forwarding rules.

To resolve any DNS queries for resources in the AWS VPC from the on-premises network, you can create an `inbound endpoint` on Route 53 Resolver and then DNS resolvers on the on-premises network can forward DNS queries to Route 53 Resolver via this endpoint.\
To resolve DNS queries for any resources in the on-premises network from the AWS VPC, you can create an `outbound endpoint` on Route 53 Resolver and then Route 53 Resolver can conditionally forward queries to resolvers on the on-premises network via this endpoint.
