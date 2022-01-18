### Database Migration Service (DMR)

AWS Database Migration Service helps you migrate databases to AWS quickly and securely.\
AWS DMS enables you to seamlessly migrate data from supported sources to
* relational databases
* data warehouses
* streaming platforms: Amazon Managed Streaming of Kafka (Amazon MSK)
* and other data stores in AWS cloud

The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database.

Use cases:
* Homogeneous Database Migrations: Oracle -> Oracle
* Heterogeneous Database Migrations: MySql -> Postgres
* Continuous Data Replication
* Database Consolidation
* Development and Test

During migration the source database remains available.

**CDC (Change Data Capture)** - continuous date replication

A client needs to create an EC2 instances that will perform DMS task.

Data migration can be:
* Full load + CDC – The task migrates existing data and then updates the target database based on changes to the source database.
* CDC only – The task migrates ongoing changes after you have data on your target database.


#### SCT Schema Conversion Tool
In case of migration between different databases (or different major versions?) SCT is required to convert the data schema.
