### EMR (Elastic MapReduce)

EMR helps to create **Hadoop** clusters (Big Data) to analyze and process vast amount of data.

Benefit of EMR: it knows how to coordinate and configure hi number of EC2 instances to work together.\
No need to create Hadoop cluster manually.
EMR takes care of provisioning.\
EMR is integrated with auto-scaling and Spot instances.

Data for processing s uploaded to S3.

EMR also supports: Apache Spark, Apache Hive, Apache HBase, Apache Flink, Apache Hudi, and Presto.

Use cases:
* data processing
* machine learning
* web indexing
* big data

For short-running jobs, you can spin up and spin down clusters and pay per second for the instances used.\
For long-running workloads, you can create highly available clusters that automatically scale to meet demand.\
Amazon EMR uses Hadoop, an open-source framework, to distribute your data and processing across a resizable cluster of Amazon EC2 instances.

Apache Hadoop is a collection of open-source software utilities that facilitates using a network of many computers to solve problems involving massive amounts of data and computation.\
It provides a software framework for distributed storage and processing of big data using the MapReduce programming model.
