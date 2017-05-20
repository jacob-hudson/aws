# Databases

## Amazon Relational Database Service (RDS)
- Simplifies the setup, operations, and scaling of a relational database on AWS
- RDS exposes a database endpoint to which client software can connect and execute SQL, it does not provide shell access and restricts access for tasks that require advanced privileges

### Database (DB) Instances
- RDS is an API that allows the creation and management of DB instaances, which is an isolated database environment deployed in a private network segment of the cloud
- RDS currently supports
  - MySQL
  - PostgreSQL
  - MariaDB
  - Oracle
  - SQL Server
  - Aurora
- The compute and memory resources of a DB instances are determined by the class, migration between classes will happen automatically if needs change

### Operational Benefits
- RDS increases the operational reliability of databases by applying a very consistent deployment and operational model, this is achieved by limiting configurations and automation
- EC2 can be used in place of RDS if having full OS control of the database is important

### Storage Options
- RDS is built using EBS
- Options include
  - Magnetic HDD
  - General Purpose SSD
  - Provisioned IOPS SSD

### Backup and Recovery
- RDS Provides a consistent operational model for backup and recovery
- Backup Options
  - Automated Backups
  - Manual Snapshots

#### Automated Backups
- Continuously tracks changes and backs up databases
- A complete snapshot of DB instances
- Default retention period is 1 day, can be up to 35 days
- Automated backups are deleted when an instance is deleted, manual ones are not
- Automated backups occur daily with a configurable 30 minute window

#### Manual DB Snapshots
- Manual snapshots are as frequent as desired
- Created in the console or by using `CreateDBSnapshot`
- Are not removed until explicitly deleted

#### Recovery
- Snapshots can not be restored to an existing instance, a new one has to be created
- Only the default DB parameter and security groups are associated with the instance

### High Availability with Multi-AZ
- Allows database cluster creation across multiple availability zones
- Done using replication
- Primary instance is in one AZ, secondary one is in another
- RDS automatically replicates the data from the master database or primary instance to the slave database or secondary instance using synchronous replication
- RDS has automatic failover for the following
  - Loss of availability in primary AZ
  - Loss of network connectivity to primary database
  - Compute unit failure on primary database
  - Storage failure on primary database
- The DNS name will not change, the underlying CNAME will

### Scaling up and out
- Vertical
  - Done through adding additional compute, memory, or storage
  - Changes can happen at the next maintenance window or immediately
  - Done with `ModifyDBInstance`
- Horizontal Scaling with Partitioning
  - Also called sharding, allows for more users but requires additional logic at the application layer
  - NoSQL databases like DynamoDB and Cassandra are designed to scale horizontally
- Horizontal Scaling with Read Replicas
  - Read replicas offload read traffic from the primary database to increase the number of transactions
  - Use cases of read replicas
    - Scaling beyond capacity for read heavy workloads
    - Handling read traffic while the source DB is down
    - Offloading reporting or data warehousing

### Security
- Protect infrastructure using IAM
- Deploy RDS into a private subnet in VPC
- creating users and granting permissions with strong passwords that are rotated frequently
- Protect the data using SSL, KMS, or TDE; All logs, backups, and snapshots are encrypted for an encrypted RDS instance

## Amazon Redshift
- Fast, powerful, fully managed, petabyte-scale data warehouse Service
- Relational database designed for OLAP and reporting of very large datasets

### Clusters and Nodes
- A cluster is composed a of a leader node and one or more compute nodes
- Compute options are split into two types
  - Dense Compute
  - Dense Storage
- Each cluster is one or more databases
- Performance can be improved by adding more nodes to a cluster
- When a query is ran, it is executed across all nodes in parallel
- Data is spread across the nodes in a distribution strategy that is specified by the user
- During resizing, data will become read-only until the operation is complete

### Table Design
- Data Types
  - Common supported Types
    - Integer
    - Decimal
    - Double
    - Char
    - Varchar
    - Date
    - Timestamp
  - Additional data can be added with `ALTER TABLE`, but existing columns can not be modified
- Compression Encoding
  - When being initially loaded, data is sampled and the best compression scheme is chosen for each column
  - It can be manually specified on a per-column basis as part of `CREATE TABLE`
- Distribution Strategy
- Determines how data should be partitioned to best meet patterns
- The goal is to minimize impact of redistribution by putting the data where it needs to be before getting queried
- Has a huge impact on performance
- When creating a table, choose one of the following distribution styles
  - Even - Default, results in data being uniform across slices regardless of the data
  - Key - Rows are distributed according to values in one column, matching values will be stored closer together
  - All - A full copy is on every node, best used for information that is not updated often

### Sort Keys
- Sorting enable effective handling of range-restricted predicates, allowing the query to skip over large number of blocks during scans
- The sort keys can be compound or interleaved
  - Compound is better when the predicate uses a prefix
  - Interleaved gives equal weight to each column

### Loading Data
- Redshift supports standard SQL commands in addition to `COPY`, which reads multiple files on S3 at once
- After a bulk load, perform a `VACUUM` to organize data and reclaim space
- Data can be exported using `UNLOAD`

### Querying Data
- Querying tables is done with SQL commands
- CloudWatch and the Redshift console can be used for performance monitoring
- Large workloads can use WLM, concurrency can also be managed with WLM

### Snapshots
- Function similar to RDS

### Security
- Very similar to RDS

## DynamoDB
- Fully managed NoSQL database
- All data is stored on high-performance SSD drives
- Monitoring is done with CloudWatch

### Data Models
- Basic components include tables, items, and attributes
  - A table is a collection of items
  - An item is a collection of attributes
- Each attribute is a key/value pair

### Data Types
