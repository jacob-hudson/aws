# Elastic Load Balancing

## Basics
- A load balancer is a mechanism that automatically distributes traffic across multiple EC2 instances
  - It can be a self-managed virtual load balancer on EC2 instances or use Elastic Load Balancing, which is a managed load balancer
- The ELB service does work across availability zones, enabling high availability (HA)
- ELB supports routing and load balancing of HTTP/S, TCP, and SSL; and provides a single CNAME entry point for DNS
- Advantages to ELB
  - Managed service that scales in and out automatically to meet demand
  - Helps achieve HA by distributing traffic across healthy instances in multiple Availability Zones
  - Seamless integrations with Auto Scaling to scale EC2 instances
  - Works with VPC, allowing only Internet facing public IP to be exposed
  - Supports integrated certificate management and SSL termination

## Types of Load Balancers
- Internet-Facing
  - Takes requests from the Internet and distributes them to registered EC2 instances
  - Receives a public DNS name that clients use to send requests, this gets resolved to the balancer's public IP address, which can be visible to client applications
  - It is not recommended to bind an application to an IP address, as it may not be part of the resource pool in the future
  - ELB in a VPC supports IPv4, ELB in EC2-Classic supports IPv4 and IPv6
- Internal Load Balancers
  -  In multi-tiered apps, it may be useful to load-balance between the tiers (eg between the web and app tier)
- HTTPS Load Balancers
  - SSL/TLS can be used to load balance, this enables traffic encryption between the load balancer and clients that iniate HTTPS sessions and for connections between the load balancer and back-end instances
  - ELB provides predefined SSL configurations to negotiate connections between clients and the load balancer
  - In order to use SSL, a SSL certificate must be installed on the load balancer that it uses to terminate the connection and decrypt requests from clients before sending them to the back-end EC2 instances
    - Optional back-end authentication is available
  -ELB does not support SNI on the load balancer, meaning hosting multiple websites on a fleet of EC2 instances behind ELB with a single SSL certificate, a SAN is needed for each website and added to the certificate to avoid warning messages when the site is accessed

## Listeners
- Every load-balancer must have one or more listeners attached, which are processes that check for connection requests
- ELB supports the following:
  - HTTP
  - HTTPS
  - TCP
  - SSL
- ELB Operates on Layers 4 and 7 of the OSI Layer

## Configuring Elastic Load Balancing
- Idle connection timeout
  - Each request results in two connections, one to the client and the other to the backend
  - ELB defaults the timeout to 60 seconds for both connections
  - If HTTP or HTTPS listeners are used, enable keep-alive for the EC2 instances, it allows the load balancer to reuse connection to the backend (reducing CPU utilization)
- Cross-Zone Load Balancing
  - Cross-Zone load balancing reduces the need to maintain equivalent numbers of backend instances in each zone and improves ability to handle to loss of one or more backend instances
- Connection Draining
  - Connection draining ensures that requests do not go to unhealthy or deregistering instances
  - The time specified to keep instances alive before draining them can be set between 1 and 3600 seconds with a default of 300 seconds
- Proxy Protocol
  - Proxy Protocol adds a human-readable header to the request with connection information (IPs and port numbers)
- Sticky Sessions
  - Allows for binding of a user session to an instance, ensuring all requests from that user go to the same instance for that session
  - Uses own session cookie or an AWS provided one called AWSELB
- Health Checks
  - Healthy services are InService, unhealthy ones are OutOfService
  - Health checks are performaned on all registered instances
  - Example health Checks
    - Ping
    - Connection attempt
    - Page that is checked periodically
  - The time interval between checks and the response wait time can be turned, as well as the number of failures before a service is unhealthy
