# S3 (Simple Storage Service)

## Traditional Storage
- block level
  - Operates at a lower level
  - Manages data as fixed-sized blocks
  - Accessed over a Storage Area Network (SAN) using protocols such as iSCSI or Fibre Channel
- file
  - Operates at the OS level
  - Manages data as a named hierarchy of files and folders
  - Accessed over a NAS using protocols such as CIFS or NFS

## Object Storage
- Not closely associated with a server
- Data is accessed through an API build on HTTP verbs
- Contain both data and metadata
- Reside in buckets

## Buckets
- Flat folder structure (no sub-buckets)
- Every bucket can hold an unlimited number of objects
- Bucket names are global across _all_ AWS accounts
  - *It is suggested to use bucket names that contain your domain name and conform to rules for DNS names.  This ensures buckets can be used in all regions and host static websites, if desired*

## S3 Overview
- Highly-durable and highly-scalable object storage that is optimized for reads

## AWS Regions
- Buckets are placed in a user-defined region

## Objects
- May be virtually any kind of data and up to 5TB in size
- Every object has data and metadata
  - data: the file itself
    - the data portion is opaque, meaning it is treated as a stream of bytes regardless if it is text or binary
  - metadata: data about the data in key/value pairs
    - Two types of metadata:  System and User
      - System:  Created by S3, contains things like thedate last modified, object size, MD5 digest, and HTTP Content-Type.
      - User:  Optional and can only be specified at creation time
- Unique identification is via the bucket, key, and optional version ID

## Object URL
- Formed form the web services endpoint, bucket name, and the object key
- May contain delimiting characters like forward and backslashes for logical organization

## Keys
- Keys are unique IDs for S3 (similar to a file name).
  - Can be up to 1024b of UTF-8 characters
  - must be unique _only_ the bucket

## API
- Commands
  - Create/delete bucket
  - Write an object
  - Read an object
  - Delete an object
  - List keys in a bucket
- All done via a REST interface
  - Usually done at a higher level though as SDK, the CLI, or Management Console

## Durability and Availability
- Durability: Will my data be there in the future?
  - 99.999999999% (11 9's) of Durability
  - This is possible via automatic redundancy on multiple devices in multiple facilities in a given region
- Availability:  Can I access my data right now?
  - 99.99% Availability
- Data is _eventually consistent_

## Access Control
- By default, only the person who creates the bucket or object has access
- Course-grained Control
  - ACLs
- Fine-grained Control
  - S3 bucket policies
  - IAM
  - query-string authentication
-Bucket policies are different from IAM (IAM section)
  - Assocated with a bucket resource instead of an IAM principle
  - Include an explict reference to the IAM principal in the policy, the principle can be associated to multiple AWS accounts (for cross-account access)

## Static Websites
- No server side scripting
- Straightforward due to every object having a URL
- Simply configure the bucket for web hosting and upload Content
- The URL will be `<bucket-name>.s3-website-<AWS-region>.amazonaws.com.`

## EBS and EFS Overview
- EBS is Traditional block and file storage for AWS
- EFS provided network-attached shared file storage using NFS v4
