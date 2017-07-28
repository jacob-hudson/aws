# CloudFront

## Overview
- CloudFront is a CDN - A globally distributed network of caching servers that speed up downloading of webpages and other content.
- This is done using DNS geo-location, determining where the request is coming from and using the closest edge server to serve content
- A CDN helps spikes and is transparent to end users
- Users immediately receive content on the edge servers
- Content not on the edge server is fetched from the origin server (eg S3)
- CloudFront integrates with many AWS services and can use non-AWS origin servers
- CloudFront supports any content that can be sent over HTTP or HTTPS

## Basics
- Major concepts
  - Distributions
    - A domain name used for CloudFront
    - Replaces actual domain name
    - Can be used as-is or replaced via a CNAME with a DNS provider
  - Origin
    - Needed when creating a distribution
    - Is there the definitive version of the files reside
  - Cache Control
    - Once requested and served, objects in an edge location stay cached until they expire or are evicted to make room for more frequently requested content
    - Default - 24 hours
    - The next request after an object expires is forwarded to the origin to verify the object is unchanged or fetch a new version if it has changed.
    - Copies of an object can be removed via the `invalidation` API, which removed the object from every edge location
    - A good practice is versioning, users will see new content and the old will expire without invalidation

## Advanced Features
- Dynamic Content, Multiple Origins, and Cache Behaviors
  - Content and Origins are controlled via Cache Behaviors
  - Cache patterns are driven based on URL patterns (eg one pattern for PHP and another for JPEG)
  - Example functionalities for Cache Behaviors include
    - Path patterns
    - Which origin will requests be forwarded too
    - Whether to forward query strings
    - If a signed URL is needed
    - Requiring HTTPS access
    - The amount of time a file may stay in CloudFront
  - Path patterns are in order, with the last one being * (default)
- Whole website
  - With cache behaviors and multiple origins, using CloudFront to server an entire website is easy
- Methods to restrict private content
  - Signed URLs - Only valid between certain times and/or IP addresses
  - Signed Cookies - requires authentication between public and private keepers
  - Orign Access Identities (OAI) 0 Restrict access to a S3 bucket only to a special CloudFront user associated with a distribution, ensuring a bucket's content is only accessed by Amazon CloudFront

## Use Cases
- Good examples
  - Serving the Static Assets of Popular Websites
    - Including images, CSS, and JavaScript
  - Serving a whole website or application
    - Can be possible using multiple origins, cache behavior, and short TTLs for dynamic content
  - Serving contents across a wide geographic area
    - Will significantly improve performance for distant users and reduce the load on the origin server
  - Software distribution
    - Will speed up downloads
  - Streaming Media
    - Can serve audio and video
- Bad examples
  - Most requests come from a single location
    - Multiple edge locations will prove useless
    - Mimics a few locations, so edge servers will be useless
