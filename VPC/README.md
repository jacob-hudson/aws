# VPC (Virtual Private Cloud)

## VPC Basics
- Networking layer for EC2, allows to build own networks in AWS
- Multiple VPCs can be created in a region and all are isolated (even if they share an address space)
- A CIDR block must be specified when creating a VPC, with a largest possible side of /16 and a smallest size of /28
- A VPC contains
  - Subnets
  - Routing Tables
  - DHCP option sets
  - Security groups
  - ACLs
  - Optionally
    - IGWs
    - EIP Addresses
    - ENIs
    - Endpoints
    - Peering
    - NAT instances and gateways
    - VPGs, CGWs, and VPNs

## Subnets
- VPC IP range where services can be launched
- CIDR Blocks define Subnets
- The first four and last usable host address are reserved by AWS for internal networking purposes
- Subnets are in one Availability Zone and can not span between zones, however one Availability Zone can have multiple Subnets
- Classifications include Public, Private, and VPN only
  - Public - The associated routing table directs traffic to the IGW
  - Private - The associated routing table DOES NOT direct traffic to the IGW
  - VPN Only - The associated routing table directs traffic to the VPG and does not have an IGW
- VPC default is one public subnet in every Availability Zone within the region, with a netmask of /20

## Routing Tables
- Logical construct within a VPC that contains a set of rules (routes) that are applied to the subnet and determine where traffic is directed
- Routes permit EC2 instances within different subnets within a VPC to communicate, they are also used to specify public and private subnets
- Each route table contains a default route called the local route, this enables communication within the VPC (the route can not be modified or deleted)
- Additional routes can be added to direct traffic out of the VPC via the IGW, VPC, or NAT instnace
- Key Points
  - VPC has an implict router
  - VPC automatically comes with a default route that can not be deleted or modified
  - Additional routes can be created
  - Each subnet must be associated with a route table, which controls routing for the subnet..  If a subnet isn't explicitly associated with a route table, the subnet uses the main route table
  - The main route table can be replaced with a custom one so that each new subnet is automatically associated with it
  - Each route specifies a destination CIDR and a target (eg the VPG).  AWS uses the most specific matching route to direct traffic

## Internet Gateways (IGWs)
- An IGW is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in the VPC and the Internet
- An IGW provides a target for Internet-routed traffic in a VPC, NAT is performed for instances that have been assigned public IP addresses
- EC2 instances within a VPC are only aware of their private IP addresses, meaning Internet-bound traffic gets translated at the IGW to a public IP or EIP, the one-to-one mapping is retained
The following must be done to create a public subnet with Internet access
  - Attach an IGW to your Amazon VPC
  - Create a subnet route table rule to send all non-local traffic (0.0.0.0) to the IGW
  Configure ACLs and security group rules to allow relevant traffic to flow to and from the instance
- A public or EIP address must be assigned to send and receive traffic from the Internet

## DHCP Option Sets
- The Options Field of a DHCP Message contains the configuration parameters, some examples are:
  - Domain name
  - DNS Server
  - netbios-node-type
- AWS Automatically creates and associated a DHCP option set for your Amazon VPC upon creation and sets two options:
  - Domain name servers (defaults to AmazonProvidedDNS)
  - Domain name (Default for the domain name of the region)
- To assign a custom domain name to resources, create a custom DHCP option set and assign it to the VPC
- The following DHCP Options values may be configured
  - domain-name-servers - The IP of up to 4 DNS servers
  - domain-name - The desired domain name
  - ntp-servers - The IP of up to 4 NTP servers
  - netbios-name-servers - The IP of up to 4 NetBIOS Servers
  - netbios-node-tupe - Set to 2
- Every VPC must have only one DHCP option set assigned to it

## Elastic IP addresses (EIPs)
- EIPs come from a pool of public IP addresses maintained by AWS that can be associated to resources within a VPC
- An EIP is a static public IP address in the pool for the region of the VPC that can be allocated (pulled) from the pool and released (returned) to the pool
- EIPs remain fixed while the underlying infrastructure may change over time
- Main points:
  - EIPs must first be allocated for use within a VPC and then assigned to an instance
  - EIPs are specific to a region (an EIP in one region can not be assigned to another region)
  - There is a one-to-one relationship between network interfaces and EIPs
  - EIPs can be moved between instances, either in the same VPC or a different VPC in the same region
  - EIPs remain associated with an AWS account until explicitly released
  - There are charges for EIPs allocated with the account, even when they are not associated with a resource

## Elastic Network Interfaces (ENIs)
- An ENI is a virtual network interface that you can attach to an instance in a VPC
- ENIs are only available within a VPC and they are associated with a subnet upon creation
- An ENI may contain one public and multiple private IP addresses
  - If there are multiple private IP addresses, one of them is primary
- Assigning a second network interface to an instance via an ENI allows it to be dual-homed (have networking in different subnets)
- An ENI created independently of a particular instance persists regardless of the lifetime of any instance to which it is attached
  - If an instance fails, the IP address my be preserved by attaching the ENI to another instance
- ENIs allow for the creation of a management network, use network and security appliances in the VPC, create dual-homed instances, or create a low-budget and high availability solution

## Endpoints
- Endpoints allow for private connections between Amazon VPC and another AWS service without requiring access over the Internet or through a NAT, VPN, or AWS DirectConnect
- Multiple endpoints can exist for a single service and different route tables can enforce different access policies from different subnets to the same service
- VPC endpoints currently support communication with S3, other services are expected to be added in the future
- To create an endpoint:
  - Specify the VPC
  - Specify the service, it can be identified with the prefix `com.amazonaws.<region>.<service>`
  - Specify the policy, either full access or a custom policy (the policy can be changed at any time)
  - Specify the route tables, a route will be added to each specified route table, which will state the service as the destination and the endpoint as the target

## Peering
- A peering connection is a networking connection between two VPCs that enables instances in either VPC to communicate with each other as if they are on the same network
- Peering connections can be created between two VPCs on the same or different AWS accounts within a single region
- Peering connections are not gateways nor a VPN connection and do not introduce a single point of failure for communication
- Peering connections are through a request/accept protocol
  - The owner of the requesting VPC sends a peer request within one week
  - If both VPCs are in one account, the request contains the VPC ID
  - If the VPCs are in different accounts, the request contains the Account ID and VPC ID
- VPCs may have multiple peering connections, peering is a one-to-one relationship between VPCs
  - Meaning two VPCs cannot have two peering agreements between them.  Peering connections do not support transitive routing
- Main points about peering:
  - A peering connection can not be created between VPCs that have matching or overlapping CIDR Blocks
  - Peering connections can not be created in different regions
  - Peering connections do NOT support transitive routing
  - Only one peering connection between the same two VPCs at the same time
  
