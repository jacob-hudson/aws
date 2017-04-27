# EBS (Elastic Block Store)

## Basics
- Persistent block level storage
- Volumes are automatically replicated within an availability zone
- Multiple EBS volumes can be attached on one EC2 instance

## Types of volumes
- Magnetic
  - Lowest performance and lowest price
  - Size from 1 GB to 1 TB
  - Averages 100 IOPS, can burst to hundreds of IOPS
  - Ideal for
    - Data that is accessed infrequently
    - Sequential reads
    - Where low-cost storage is a requirement
  - Billed based on the space provisioned, not the usage
- General-Purpose SSD
  - Cost-effective storage that is ideal for a broad range of workloads
  - Size from 1 GB to 16 TB
  - Baseline IOPS is 3 per GB, capped at 10,000
    - Volumes under 1 TB can burst up to 3,000 IOPS for an extended period (due to I/O credits that are gained when IOPS is not used)
  - Ideal for
    - System boot Volumes
    - Small to medium Databases
    - Dev and test environments
  - Billed based on the space provisioned, not the usage
- Provisioned IOPS SSD
  - Designed for I/O intensive workloads, particularly databases
  - Size from 4 GB to 16 TB
  - Desired IOPS is specified when the storage is provisioned
    - Maximum is lower between 30 times the volume in GB or 20,000
    - Striping can be used for even more performance
  - Ideal for
    - Critical business applications that required sustained IOPS
    - Large database workloads

## EBS Optimized Instances
- Uses an optimized configuration stack and provides additional, dedicated capacity for EBS I/O
- This provides the best performance by minimizing contention between EBS I/O and other traffic
- EBS Optimzied Instances incur an an additional hourly charge

## Protecting Data

## Backup/Recovery (Snapshots)
- Snapshots are incremental backups
- Data for Snapshots in stored in S3
  - The snapshot itself is free, only the storage is used is charged
- Snapshots are created immediately and the volume may continue to be used
- Snapshots are stored in AWS-controlled storage, _not_ the customer account!
  - Snapshots are manged through the EBS Snapshot Service
- Snapshots are constrained to the region in which they originated, but can be copied to other regions
- Creating a volume from a snapshot is instant, but the data is loaded lazily

## Recovering Volumes
- Unless the `DeleteOnTermination` flag has been set to false, the volume should be detached before the instance was terminated.  It can then be added as a data volume for another instance, where the data can be read and recovered.

## Encryption
- KMS creates a master key (unless one is specified) upon instance creation
- Data and keys is encrypted with AES-256
- The data is encrypted on the servers that host EC2 instnaces, so the data is actually encrypted in transit between the host and storage media and on the media itself
- Encryption is transparent (No IOPS drops)
- Snapshots created from encrypted volumes are automatically encrypted
