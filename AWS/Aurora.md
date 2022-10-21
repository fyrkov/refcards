### Aurora
Aurora is a proprietary AWS DB cluster backed by Postgres or MySQL.\
Aurora is cloud optimized and outperforms plain RDS.\
Aurora scales automatically up to 128TB by 10GB increments.\
Replication lag is lower than RDS (sub 10ms) and there can be up to **15** RRs.
Aurora costs 20% more than RDS.

After cluster creation it is possible to add:
* RRs
* cross-region replicas
* replica auto-scaling
* AWS region (making global Aurora)

Aurora maintains 6 copies of data across 3 AZs.\
4/6 copies required to write, 3/6 required to read.\
Aurora supports cross region replication.

There are 2 endpoints. `Writer endpoint` refers to master.\
`Reader endpoint` connects to RRs and they are load-balanced.

Aurora can be created in 2 setups:
* `provisioned` - default, requires some prior cluster configuration
* `serverless` - automated DB scaling based on actual usage. Use case: infrequent unpredictable workloads

#### Fault tolerance for an Aurora DB cluster
Aurora can be created in 2 replication setups:
* `single master`

![AuroraArch001.png](files/AuroraArch001.png)

* `multi-master` (available for MySQL). Every node is a writer.\
Writes are accepted after a positive confirmation from a quorum of storage nodes.\
Replication is peer-to-peer.\
Unlike the single master mode where a fail of writer triggers RRs promotion process,\
multi-master would require an application just to switch to another writer node\
=> **hi availability** + **hi uptime** + **immediate failover**

![multi-master-aurora](files/multi-master-aurora.jpg)

If the primary instance in a DB cluster using single-master replication fails,\
Aurora automatically fails over to a new primary instance in one of two ways:
* By promoting an existing Aurora Replica to the new primary instance
* By creating a new primary instance

You can customize the order in which your Aurora Replicas are promoted
to the primary instance after a failure by assigning each replica a `priority`.\
Priorities range from `0` for the first priority to `15` for the last priority.\
If the primary instance fails, Amazon RDS promotes the Aurora Replica with the better priority to the new primary instance.\
You can modify the priority of an Aurora Replica at any time. Modifying the priority doesn't trigger a failover.

More than one Aurora Replica can share the same priority, resulting in promotion tiers.\
If two or more Aurora Replicas share the **same priority**, then Amazon RDS promotes the replica that is **largest in size**.\
If two or more Aurora Replicas share the same priority and size,
then Amazon RDS promotes an arbitrary replica in the same promotion tier.

##### Global setup
1 primary region + up to 5 secondary RO regions with replication lag is under 1 sec.\
Up to 16 RRs per secondary region.\
Secondary region can be promoted to master in < 1 min.

Use cases: decreased latency globally, hi redundancy + hi availability.
