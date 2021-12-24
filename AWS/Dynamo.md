### DynamoDB

Fully managed NoSQL DB with following features:
* Hi avail, hi throughput, lo latency
* Hi storage capacity
* Lo cost
* Multi-AZ replication
* Scales horizontally, autoscaling
* Integrated with IAM for security
* Event driven programming with DynamoDB streams
---
* Each DB consists of tables.
* Each table can have infinite number of rows.
* Each table has a **Primary Key** defined beforehand.
* Each item has **Attributes**(=columns) which can be added over time - flexible data scheme.
* Max size of an item 400Kb (not good for large objects).
* Data types:
  * Scalar types: String, Number, Binary, Boolean, Null
  * Document types: List, Map
  * Set types: String set, Number set, Binary set
* Read/Write capacity mode can be set to:
  * Provisioned mode: client specifies number of reads/writes per sec. Planned beforehand. Paid for provisioned RCU (Read Capacity Units) and WCU. It is possible to enable auto-scaling for RCU and WCU.
  * On-demand mode: no capacity planning needed. Reads/writes scale automatically. More expensive. Good for unpredictable workloads.

#### On Keys
The **primary key** uniquely identifies each item in the table.\
Types of primary keys:

* **Partition key**. A simple primary key, composed of one attribute. DynamoDB uses the partition key's value as input to an internal hash function. The output from the hash function determines the partition (physical storage internal to DynamoDB) in which the item will be stored.

* Partition key and sort key – a **composite primary key** composed of two attributes: partition key and the sort key. All items with the same partition key value are stored together, in sorted order by sort key value. In a table that has a partition key and a sort key, it's possible for multiple items to have the same partition key value. However, those items must have different sort key values.

#### Secondary Indexes
You can create one or more secondary indexes on a table. A secondary index lets you query the data in the table using an alternate key, in addition to queries against the primary key. DynamoDB doesn't require that you use indexes, but they give your applications more flexibility when querying your data. After you create a secondary index on a table, you can read data from the index in much the same way as you do from the table.

DynamoDB supports two kinds of indexes:
* Global secondary index – An index with a partition key and sort key that can be different from those on the table.
* Local secondary index – An index that has the same partition key as the table, but a different sort key.

Each table in DynamoDB has a quota of 20 global secondary indexes (default quota) and 5 local secondary indexes.

#### DAX Dynamo Accelerator
In-memory cache for Dynamo.\
Seamless integration - no changes from the app POV.\
5 min TTL for cache (default).

#### DynamoDB streams
Ordered stream of item-level modification (create, update, delete) in a table.\
Stream records can be:
* Sent to Kinesis Data Streams
* Read by AWS Lambda
* Read by Kinesis Client Lib apps

Retention time: 24hr.

Use cases:
* welcome email to new users

#### Global tables
Tables are across multiple regions.\
Implemented with two-way replication between Dynamo tables in different regions.\
All tables are masters.\
Dynamo Streams as a mandatory pre-requisite.

Use case: make Dynamo lo latency in multiple regions

#### Items TTL
Expiry time attribute allows items to be automatically removed.\
Use cases:
* legal
* reducing costs

#### Transactions
Allow to write to two tables at the same time transactionally.
