# Route 53

## DNS Primer
- Hierarchal structure, levels separated with a dot (.)
- Example:  Amazon.com
  - com is the TLD
  - Amazon is the SLD
- Route 53 is authoritative - it provides an update mechanism that developers use to manage public DNS names

## DNS Concepts
- TLDs
  - Most general part of a domain name, also is the farthest to the right
  - Managed by ICANN
- Domain Names
  - Human friendly name, eg Amazon.com
- IP Addresses
  - Unique network addressable location
  - IPv4 (32 bit) and IPv6 (128 bit) are both common
- Hosts
  - Separate computers or services available through a domain
  - Can have a definition (eg www in www.amazon.com)
  - Host names can be arbitrary if they are unique for the domain
- Subdomains
  - Can mean the SLD, as a Subdomain of the TLD (google in google.com)
  - Usually prefixes the SLD - history.illinois.edu
  - Difference between a host and a Subdomain
    - A Host is a specific computer or resources
    - A Subdomain extends the parent domain, as in subdividing the domain itself
    - In both cases, the left-most portion of the domain is the most specific
- Fully Qualified Domain Name (FQDN)
  - Absolute domain names
  - Specifies domains relation to the absolute root of the DNS
    - Meaning each parent domain is specified, including the TLD
    - Example (conforming to ICANN standards) - api.aws.amazon.com.
    - The dot at the end in the root
- Name Servers
  - Computer designed to translate domain names into IP addresses
  - Name servers can be authoritative - they give answers to queries
- Zone Files
  - A text file that contains the mappings between domain names and IP addresses
  - They reside on name servers and generally define resources available under a specific domain or where to get that information
- TLD Name Registrars
  - Organization or commercial entity that manages the reservation of Internet domain names
  - Registrars must be accredited by a gTLD or ccTLD

## Steps in DNS Registration
- Hosts file is checked first
- DNS cache is checked next to see if site has been visited
- Root servers don't know where to domain is hosted, but they can direct requesters to name servers for a specific TLD
- For TLDs, a new request is sent to the correct server based on the TLD (eg `.org` for `wikipedia.org`)
- An IP for `wikipedia.org` will not be found, but there will be Domain-Level name servers for wikipedia
- At the domain level name servers, the requester has the IP address of the name server that is responsible for knowing the actual IP address.   A new request is sent for `wikipedia.org` again
- The name server checks it's zone files and finds one for Wikipedia, which contains a record for the IP address of the www host which gets returned to the requester
  - The requester is a resolving name server - a server configured to ask other servers questions

### Zone files
- How name servers store information about the domains they know.
- Most requests to the average name server are for domains that are not in the local zone files
- The zone file's `$ORIGIN` directive is a parameter equal to teh zone's highest level of authority by default

## Record Types
- A record is a single mapping between a resource and a name
- Types of records
  - SOA - Mandatory in all zone files, it identifies the base DNS information about the domain.  Each sone contains a single SOA record.  
  - A - Map a host to an IPv4 address
  - AAAA - Map a host to an IPv6 address
  - MX - Used to define mail servers for a domain and ensure emails messages are routed correctly, it should point to a host via an A or AAAA record and not a CNAME record
  - NS - Used by TLD servers to direct traffic to DNS servers that contain authoritative DNS records
  - PTR - Essentially the reverse of an A record, it maps an IP address to a DNS name (it is mainly used to check if the server name is associated with the IP address from where the connection was initiated)
  - SPF - Used by mail servers to combat spam, it does this by informing mail servers what IPO addresses are authorized to send an email from a domain name (It prevents the domain name from being spoofed)
  - TXT - Used to hold text information, can be used to provide human readable information with a host
  - SRV - Specification of data in the DNS defining the location of servers for specified services

# Amazon Route 53 Overview
- Route 53 is a highly available and scalable cloud DNS web service that is designed to give developers and businesses an extremely reliable and cost effective way to route end users to Internet applications
- Main functions
  - Domain registration - Registering domain names, like `example.net`
  - DNS Service - Translates domain names into IP addresses using a global network of authoritative DNS servers
  - Health checking - Automated requests are sent to apps in order to verify they are reachable, available, and functional

## Domain Registration
- Domain names can be register with Route 53 or transferred from another domain registrar
- A complete list of TLDs can be found here - https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html

## DNS Service
- Route 53 is an Authoritative DNS Service
- When someone enters your domain name into their browser, a DNS request is forwarded to the nearest Route 53 DNS server, Route 53 will respond with the IP address specified for the given host
- If you registered a new domain with Route 53, it will automatically be configured as the DNS service for the domain (a hosted zone will be created for your domain).  
- Route 53 can route traffic to CloudFront, S3, or ELB

## Hosted zones
- A hosted zone is a collection of resource record sets hosted by Route 53
- Main Types
  - Private - contains information about how traffic should be routed within one or more VPCs
  - Public - contains information about how traffic should be routed on the Internet
- Resource record sets contained in a hosted zone must share teh same suffix

## Supported Record Types
- Supported Record Types
  - A
  - AAAA
  - CNAME
  - MX
  - NS
  - PTR
  - SOA
  - SPF
  - SRV
  - TXT
  - Routing Policies
- Routing policies determine how Route 53 responds to queries
- Policy Options
  - Simple
  - Weighted
  - Latency-based
  - Failover
  - Geolocation
- Health checks are part of routing policies, meaning unhealthy hosts do not receive traffic

### More on each type
- Simple
  - Default policy
  - Used for a single resource that performs a given function
  - Route 53 responds based only on the values in the resource record (eg A)
- Weighted
  - Multiple resources can be associated with a single DNS instance
  - Used when multiple resources can perform the same functions
    - Can be used for load balancing or beta testing
  - Route 53 responds with one record from the group, the probability of being selected is from the following formula
  ` Weight for a given resource record / Sum of weights for the resource record set in the group`
- Latency-Based
  - Allows for routing based on the lowest network latency from the end user's POV
  - Used when multiple resources can perform the same function in multiple regions or AZ's
  - Route 53 responds with the record set for whichever resource has a lower latency from the end user
- Failover
  - Allows for active-passive failover, where one resource takes all of the traffic should the primary resource fail
  - Failover resource record sets can NOT be created for private hosted zones
  - Health checks are set for IP's and Protocols.  Route 53 will route based on the success or failure of the health checks
- Geolocation
  - Allows for traffic to be routed based on geographic locations of users (where the DNS queries originate)
  - Geolocation works by mapping IP addresses to locations.  A default record set should be created for IP addresses that are not mapped to a specific location or that do not have a specific record set
  - Route 53 will route based on the Geolocation/Record set combonation
- More on health checks
  - Health checks monitor health of resources and can be used with CloudWatch Alarms
  - Route 53 can route traffic away from unavailable resources
  - Route 53 can performa active-active and active-passive failover automatically, significantly better than monitor-alert-restart of traditional failover
  - Route 53 health checks are not triggered by DNS queries, but ran periodically by AWS with results being published to all DNS servers
  - Failovers may happen in 90 seconds, 30 for 3 failed tests and 60 for DNS TTL

## Resiliency
- Building blocks of highly available and resilient applications
  - ELB is set up with cross zone load balancing and connection draining
  - ELB delegates requests to EC2 instances running in multi AZ in auto-scaling groups.  
  - ELB Health checks are defined
  - Route 53 Health checks are defined
  - CNAMEs to Prod are pointing to ELB and uses latency-based routing
  - The failover environment has Route 53 records pointing to CloudFront or S3 for static apps
  - The web subdomain has Route 53, with Prod as primary target and DR as secondary using a failover routing policy
  - The hosted zone points to an S3 bucket for static apps
  - Content can be served using CloudFront
  - The app is deployed in multiple AWS regions
