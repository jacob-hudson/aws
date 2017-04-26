# EC2 (Elastic Compute Cloud)

## Basics
- Compute is the amount of computational power needed to accomplish a task
- Compute is acquired through instances
- Charged per hour while the instance is running
Main parts of an instance:
  - The amount of virtual hardware dedicated (Instance Type)
  - The software loaded onto the instance (AMI)

## Instance Type
- Virtual hardware supporting an EC2 instance.  Many are available with difference combos of:
  - vCPUs
  - Memory
  - Storage (size and type)
  - Networking
- Instances are grouped into families based on the hardware ratios
  - the `m4` family is a good setup for many apps
- Network Performance is in relative terms (low, medium, high) depending on the instance type, with some going up to 10 Gbps.  The network performance increases as the instance type grows.
  - Enhanced networking is an option that reduces the impact of virtualization on network performance through SR-IOV, which results in a higher Packets Per Second (PPS).
  - Enhanced networking requires the the correct drivers be installed and modifying an instance attribute.  
  - Enhanced networking is only available in instances launched in VPC

## AMIs (Amazon Machine Instances)
- AMIs define the initial software that will be on an instance when launched.
- AMIs define many aspects, including:
  - OS and its configuration
  - Initial state of any patches
  - Application or system software
- All AMIs are x86 based (Linux or Windows)
- Sources of AMIs
  - Published by AWS - similar to an ISO image
  - AWS Marketplace - Prebuilt third party configuration (may incur additional fees)
  - Generated from Existing Instances - A custom AMI based off a running EC2 instance
  - Uploaded Virtual Servers - Using the VM Import/Export Service, a VM can be migrated from another provider

## Security
- Once launched, instances can be managed over the Internet.  Many security solutions exist.

## Addressing an instance
- Many ways an instance may be addressed after creation:
  - Public DNS - Automatically generated when creating an instance.  It only persists for the length of time the instance is running and can not be transferred.
  - Public IP - Assigned by AWS and can not be specified.  It only persists for the length of time the instance is running and can not be transferred.
  - Elastic IP - IP Address that is unique to the Internet (public IP) that you reserve and associate to the EC2 instance.  This IP assignment persists until it is released, and is not tied to the lifetime or state of an individual instance.  It can be transferred and shared
  - Private IPs and ENIs are available only for Amaon VPC

## Initial Access
- EC2 uses public key cryptography to encrypt and decrypt login information through a key pair
  - The public key is used to encrypt the data
  - The private key is used to decrypt it
- Initially accessing hosts
   - Linux - Use the private key, the public key is in `~/.ssh/authorized_keys`
   - Windows  - the Administrative account password is randomly generated and encrypted with the public key and can be decrypted with the private key

## Virtual Firewall Protection
- Firewalls, called _security groups_, permit traffic to go in and out of the instance.
- Traffic can be controlled through
  - port
  - protocol
  - source/destination
- Capabilities differ between VPC and classic EC2
  - Classic EC2 can only filter outgoing traffic
  - VPC can control both outgoing and incoming traffic
- Security groups are associated with instances when they are launched.  Each instance has to have at least one security group, but can have more.
- Security groups are default deny - traffic that is not explicitly defined in a rule is not allowed in.  
- When multiple security groups are present, traffic that is allowed by each individual group is allowed in.
- Security groups are a stateful firewall, responses for outgoing messages are allowed in without an explict rule.
- Security groups are applied at an instance level, instead of the perimeter.  An attacker would have to breach the security group repeatedly for each individual instance.  

## Instance Lifecycle
- Launching
  - Bootstrapping - code that is ran on launch to provision the machine, including the following:
    - Applying patches and updated to the OS
    - Enrolling in a directory Service
    - Installing application software
    - Copying a larger script
    - Installing apps like Chef, Puppet, or the Splunk Forwarder
  - VM Import/Export - Moving VMs to EC2
  - Instance Metadata - Data about the instance, including
    - Associated security groups
    - Instance ID
    - Instance type
    - AMI used to launch the instance

## Managing Instances
- Tags - key/value pairs that are associated with an EC2 instance or other service.

## Monitoring
- AWS CloudWatch provides monitoring and alerting for EC2 instances and other AWS infrastructure.  

## Modifying an Instance
- Instance Type - Any compute settings can be changed with simple selections and a restart
- Security Groups - Can not be changed in EC2 Classic

## Termination Protection
- Termination protection prevents accidental deletion due to human error by blocking termination calls until the protection is lifted.  It does not block OS shutdown commands, termination due to auto-scaling, or termination of a Spot Instance

## Options
- Pricing
  - Charges are applied for each hour an instance is running, charges can vary based on pricing Options
  - On-demand instances - No upfront cost or reservation, most expensive and most flexible
  - Reserved - scheduled and can be paid up front or later (larger discount applied for more upfront)
  - Spot - Greatest discount, customer gets instance when the spot price is below the customer set price.  The Spot instance will run until
    - The customer terminates the instance
    - The Spot price goes above the bid price
    - There is not enough unused capacity to meet the demands for Spot instances
  - Spot instances will receive a two minute termination warning before being terminated.

## Tenancy Options
- Shared Tenancy
  - Default model for all instances
  - A single host may house instances from different customers
  - Is secure, because AWS does not overprovision and instances are fully isolated
- Dedicated instance
  - Hardware is assigned to a single customer
  - As more instances are running, more hardware may be Assigned
  - Instances that are not designated as dedicated will run on shared tenancy and will be isolated from the dedicated instances
- Dedicated Host
  - Physical server with EC2 instance capacity dedicated fully to a single customer
  - Customers have complete control over which specific host runs an instance at launch, unlike Dedicated instance that can launch on any hardware that has been assigned to the account

## Placement Groups
- Logical grouping of instances within an availability zone, enable applications to participate in a low-latency 10 Gbps network
- Recommended for applications that benefit from low latency, high throughput, or both.
  - This is network connectivity _between_ instances; in order to fully use performance, choose an instance type with enhanced networking

## Instance Stores
- _Ephemeral storage_, provides temporary block-level storage for instances.
- This storage is on disks physically attached to the host
- Ideal for temporary storage that changes frequently or that is replicated across a fleet of instances
- Size and type of instance stores depend on the instance type
- Instance stores are included in the cost of an EC2 instance, but are temporary
- Data can be lost in any of the following
  - The underlying disk fails
  - The instance stops (data persists if the instance reboots)
  - The instance terminates
