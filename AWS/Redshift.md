### Amazon Redshift
Amazon Redshift is a fully managed, scalable cloud data warehouse that accelerates your time to insights with fast, easy, and secure analytics at scale.\
Redshift differs from RDS, in its ability to handle analytic workloads on big data data sets stored by a column-oriented DBMS principle.\
Redshift allows up to 16 petabytes of data on a cluster compared to Amazon RDS's maximum database size of 16TB.

Amazon Redshift is based on an older version of PostgreSQL 8.0.2, 

Redshift uses parallel-processing and compression to decrease command execution time.\
This allows Redshift to perform operations on billions of rows at once.\
This also makes Redshift useful for storing and analyzing large quantities of data from logs or live feeds through a source such as Amazon Kinesis Data Firehose.

#### Redshift vs RDS
Customers use Amazon RDS databases primarily for online-transaction processing (OLTP) workloads, while Amazon Redshift is used primarily for reporting and analytics.

#### Redshift Spectrum
Amazon Redshift Spectrum is a feature that lets you run queries against your data lake in S3, with no data loading or ETL required.
