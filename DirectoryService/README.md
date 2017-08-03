# Directory Service

## Overview
- Managed service offering that provides directories that contain information about the organization: including users, groups, computers, and other resources
- Directory types
  - AWS Directory Service for Microsoft AD
  - Simple AD
  - AD Connector
- Handles identity management, allowing more resources to be focused on the business
- Each Directory is deployed across multiple AZ's
- Monitoring automatically detects and replaces domain controllers that fail
- Data replication and daily snapshots are automatic
-
### Microsoft AD
- Managed Microsoft AD in the cloud
- Provides similar functionality and includes integration with AWS apps
- Trust relationships can be set up with an existing domain to extend directories to AWS

### Simple AD
- Microsoft AD compatible directory from AWS, powered by Samba 4
- Supports commonly used AD features such as
  - User accounts
  - Group memberships
  - Domain joining EC2 instances running Linux and Windows
  - Kerberos-based SSO
  - Group policies
- Simple AD works with WorkSpaces, WorkDocs, WorkMail, and IAM
- Also provides daily automated snapshots to enable point-in-time recovery
- Truest relationships *CAN NOT* be set up between Simple AD and other AD domains; Other features that are not supported are:
  - DNS Dynamic Update
  - Schema extensions
  - MFA
  - LDAP
  - PowerShell AD cmdlets
  - Transfer of FSMO roles

### AD Connector
- Proxy service for connecting on-premise Microsoft AD to AWS without complex directory synchronization or the cost of hosting federation infrastructure
- Forwards login requests to the AD domain controller
- Allows for use of company credentials on AWS services
- Can use MFA via RADIUS
- All management is done on the local AD instance

### Use Cases
- Microsoft AD - Best choice for 5,000+ users and need a trust relationship set up betwen AWS and on-prem
- Simple AD - Least expensive and best for 5,000- users and don't need advanced Microsoft AD features
- AD Connector - Best choice to use existing on-prem directory with AWS
