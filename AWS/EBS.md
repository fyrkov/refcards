### EBS

Elastic Block Store - network drives that can be attached to EC2 and can persist after instance termination.\
EBS are bound to AZ but can be moved between AZs with snapshots.\
A new volume can be created from the previously created snapshot.

EBS characteristics: size, IOPS, Throughput

#### EBS volume types:
* gp2/gp3 - general purpose SSD. Gp3 is more performant. For lo latency interactive apps.
* io1/io2 - hi perf SSD. For very lo latency and sustained IOPS.
* st1 - general purpose HDD. Throughput optimized. For big data, data warehousing, log processing.
* sc1 - lo cost "cold" HDD for non-frequent ops.

HDD types are not allowed to be "boot volumes".

Io1/io2 can have **"multi-attach"** to multiple EC2 instances in the same AZ. Apps must handle concurrent access.

#### EBS encryption
Enables both encryption at rest and for data in flight between an instances and a volume.\
Enabled also for snapshots.\
Encryption uses keys from KMS (AES-256).

Unencrypted existing EBS can be encrypted through snapshot creating process.


#### EC2 instance store
It is also possible to attach a `EC2 instance store` which is unlike EBS a hi perf hardware disk.\
It is ephemeral, i.e. can not persist termination.\
It is good for buffer, cache, temporary content etc with hi IO performance.\
IOPS is higher than io1/io2 have.

You can specify instance store volumes for an instance only when you launch it.\
You can't detach an instance store volume from one instance and attach it to a different instance.\
The data in an instance store persists only during the lifetime of its associated instance.\
If an instance reboots (intentionally or unintentionally), data in the instance store persists.\
However, data in the instance store is lost under any of the following circumstances:
* instance stops
* instance hibernates
* instance terminates
