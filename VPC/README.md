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

## Security groups
- A security group is a virtual stateful firewall that controls inbound and outbound traffic to AWS resources and EC2 instances
  - All EC2 instances must be launched in a security groups
  - If a security group is not specified, then the instance will be launched in the default security group for the VPC
    - This default group allows communication between all resources within the security group and outbound traffic, while denying all inbound traffic
    - Rules for the default security group may be changed, but the group can not be deleted
- Main points
  - Each VPC may have up to 500 security groups
  - Each security group may have 50 inbound and 50 outbound rules
    - If more than 100 rules need to be applied, each network interface may be associated with up to 5 security groups
    - Unlike ACLs, only allow rules can be specified (not deny rules)
    - Separate rules may be specified for inbound and outbound traffic
    - By default, no inbound traffic is allowed (until inbound allow rules are specified)
    - By default, security groups have a rule that allows all outbound traffic.  This rule can be replaced with more specific rules if desired
    - Security groups are stateful - response traffic is not effected by the rules, unlike ACLs
    - Instances in the same security group can not communicate without rules allowing it (the default group is an exception)
    - Instances can be associated to different security groups after launch, changes are immediate

## Network Access Control Lists (ACLs)
- ACLs are stateless firewalls that operate on a subnet level
- An ACL is a numbered list of rules that AWS evaluates in order, starting with the lowest number to determine if traffic is allowed in or out of a subnet
- VPCs are created with a modifiable default ACL associated with every subnet that allows all inbound and outbound traffic.
- Default rules for custom ACLs are to deny all inbound and outbound traffic
- ACLs may match security group rules for extra security
- Every subnet must be associatd with an ACL

## Network Address Translation (NAT) Instances and NAT Gateways
- By default, any instance launched in a private subnet in a VPC is not able to communicate with the Internet through the IGW
- For common use cases, a NAT gateway opposed to a NAT instance is suggested
  - This is because the gateway provides better availability and higher bandwidth, and requires less administrative effort than NAT Instances

### NAT Instance
- A NAT instance is a Linux AMI that is designed to accept traffic from instances within a private subnet, translate the source IP to the public IP of the instance, then forward the traffic to the IGW
  - It also maintains the state of the forwarded traffic in order to return responses to the proper instance
- NAT instance names contain `amzn-ami-vpc-nat`
- To allow instances within a private subnet to access Internet resources through the IGW via a NAT instnace, do the following
  - Create a NAT security group with outbound rules that specify the needed Internet resources by protocol, IP, and port
  - Launch a NAT AMI as an instance in a public subnet, associate it with the NAT security group
  - Disable the Source/Destination Check
  - Configure the routing table to direct internet bound traffic to the NAT
  - Allocate an EIP for the NAT
- The end result allows outbound connections from the private subnet, but prevents traffic originating from the internet from coming in

### NAT Gateway
- A NAT Gateway is a AWS managed resource that is designed to operate like a NAT instance, but is simpler to manage and highly available within an availability zone
- To allow instnaces within a private subnet to access Internet resources through the IGW via a NAT gateway, do the following
  - Configure the route table to forward internet bound traffic to the NAT gateway
  - Allocate an EIP to the NAT gateway

## Virtual Private Gateways (VPGs), Customer Gateways (CGWs), and Virtual Private Networks (VPNs)
- AWS offers two ways to cnnect a data center to a VPC, VPG and CGW
- A VPG is a VPN Concentrator on the AWS side, while a CGW is a hardware or software device on the customer side
- After these two elements are up, then create a VPN tunnel to link them, it gets established after the customer side of the VPN generates traffic
  - The routing type must be specified when creating the connection
  - If the CGW supports BGP, configure the VPN for dynamic routing
  - If not, configure the connection for static routing
    - For static routing, the routes for networks that should be able to communicate to the VPG must be listed
    - Routes will be propagated through the VPC so resources on the customer side and communicate with the VPC
- AWS VPC supports multiple CGWs, each having its own VPN connection to a single VPG (many to one).  The CGW IPs must be unique within the region
- AWS will provide the inforamtion needed to configure the CGW and establish VPN connections with the VPG, the connection consists of 2 IPSec tunnels for higher availability
Main points
- the VPG is the AWS end of the VPN tunnel
- The CGW (hardware or software) is the customer end
- The VPN tunnel must be initiated from the CGW to the VPG
- VPGs support both dynamic routing (with BGP) and static routing
- the VPN connection consists of 2 IPSec tunnels for higher availability to the VPC
