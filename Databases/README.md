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
