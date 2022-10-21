### WAF Web Application Firewall

AWS WAF is a web application firewall that helps protect your web applications or APIs against common web exploits and bots.

It blocks common attack patterns, such as SQL injection or cross-site scripting.\
You can also customize rules that filter out specific traffic patterns, e.g.:\
block web requests based on the IP addresses that the requests originate from

WAF can be deployed on:
* ALB
* API Gateway
* CloudFront

A user defines Web ACL (Access Control List):
* Rules include: IPs, HTTP headers, body, URI strings
* size constraints on queries
* geo-match to block specific countries
* Rate-based rules (to count occurrences of events per IP) - for DDoS protection. Use case: bit makes too many requests from the same IP.

#### Firewall Manager
A centralized place to manage all Rules of all WAFs in your accounts of an AWS Organization:
* Common set of security rules
* WAF rules
* AWS Shield Advanced
* SGs for EC2 and ENI resources in VPC
