## Kafka core concepts

##### Topics and Partitions.
:exclamation: message order is guaranteed only within the same partition
:exclamation: offsets are relevant to a topic-partition pair
![Replication](kafka_files/Partitions.png)

##### Topics and brokers:
![Replication](kafka_files/Brokers.png)
Each broker is a "bootstrap server", i.e. upon connecting to it a consumer 
receives list of all other brokers and connects to entire cluster.
Kafka is backed by the Zookeeper.
**Zookeeper** manages brokers and perform leader election if necessary.

##### Replication factor.
![Replication](kafka_files/Replication.png)

##### Partition leader:
![Replication](kafka_files/Leader.png)

##### Producers
Producer sends data to a **topic** only and does not ask for a broker or a partition.
Upon connecting to any broker it connects to the whole cluster. 
By default (if message keys not used) messages are sent to partitions in round robin.
![Replication](kafka_files/Producers1.png)
Producers can choose an acknowledgement mode:
![Replication](kafka_files/Producers2.png)

##### Message Keys
Message keys can be specified. Messages with the same key go to the same partition, thus ensuring order between them.
![Replication](kafka_files/Message_keys.png)

##### Consumers
Consumers can subscribe to one or more **partitions** within the **topic**.
So effectively number of consumers should be <= number of partitions.
![Replication](kafka_files/Consumers.png)

##### Consumer groups
Consumers can be aggregated in consumer groups.
Each consumer group consumes messages independently, i.e.  
![Replication](kafka_files/Consumer_groups.png)

Each consumer group has a service topic named `__consumer_offsets` where it stores current offsets for partitions.
When a consumer commits an offset it is stored in this topic.

##### delivery semantics
:exclamation: It is important usually to make Kafka Consumers idempotent!
![Replication](kafka_files/Delivery_semantics.png)
