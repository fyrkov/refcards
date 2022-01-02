### GuardDuty
Intelligent threat discovery to protect AWS account
* uses ML for anomaly detection
* 1 click to enable, no need to install/configure

Input data includes:
* CLoudTrail logs: unusual API calls, unauthorized deployments.
* VPC Flow logs: unusual internal traffic, unusual addresses.
* DNS logs
* Can protect against crypto currency attacks

User can set up CloudWatch Event rules to be notified of findings.\
CloudWatch Event rules can target Lambda or SNS.
