### EC2

EC2 User Data script.\
The script is run once when a machine starts.
It is a bash script with `sudo` rights.

EC2 instances parameters: vCPU, RAM, Storage, Network speed, EBS bandwidth.

EC2 instance types: `m5.2xlarge`, where `m` - class, `5` - generation, `2xlarge` - size

|Purpose|Classes|Use cases|
|--|--|--|
|General|G, M,..|
|Compute|C|CPU loading: Batch processing workloads, hi performance servers, media transcoding|
|Memory Optimized|R,X,..|Large datasets in memory, in-memory databases, cache stores|
|Accelerated Computing|P,G,..|floating point calculations, graphics processing|
|Storage Optimized|I,D,H|Lo latency hi throughput IO: Databases, distributed filesystems|

Instances types comparison chart: https://instances.vantage.sh/


#### EC2 Instance connect
It is possible to use "EC2 Instance connect" from a browser from the console page instead of using SSH.\
This still relies on the open ssh port.

#### IAM role
An EC2 can have an attached IAM role (!=SG) and therefore can be authorized to perform action towards AWS resources.

#### Security Groups
SGs contain only `allow` rules.\
SGs allow by IP ranges/ports or by other SGs.\
SGs contain inbound and outbound rules.\
SGs are locked down to a region/VPC combination.\
EC2 or other resource can have several SGs.

Usually connection timeouts indicate SGs misconfiguration.

#### EC2 Instance provision types
* "On demand" (billing per second). High cost, no upfront payment, no commitment.
* "Reserved" (1 year minimum), also Scheduled Reserved. Up to 75% discount. Can be paid upfront.
* "Convertible Reserved" can change types.
* "Spot instance" are unused EC2 capacity that can be bid on and claimed when they become available. Most cost efficient. For short workloads like batch jobs. Spot price changes over time and are different per AZ. An instance is lost when the cost exceeds max budget price.
* "Dedicated host" - a dedicated physical server. For compliance reqs and server-bound software licenses. For 1 or 3 years period.
* "Dedicated instance" - a soft version of dedicated host. No insight into the underlying sockets and cores.


#### EC2 placement groups:
* cluster - all together in one rack. For HPS and hi throughput
* spread - distributed between AZs. For reducing risks, for  critical apps.
* partition - for Kafka, Hadoop, Cassandra etc
