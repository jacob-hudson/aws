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
