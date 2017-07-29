# Storage Gateway

## Overview
- Connects an on-premise app with cloud storage, allowing apps to leverage AWS infrastructure
- Supports industry standard storage protocols
- Works with S3 and Glacier
- Software app is a VM image that gets installed in a data center
  - Exposed storage appears as an iSCSI device
- Three major configurations
  - Gateway-Cached Volumes
    - Allows expansion of local storage into S3
    - All data in a GC volume is moved to S3, while recently red data is retained locally for faster access
    - Max size is 1 PB (32 volumes, 32 TB each)
    - Point-in-time snapshots can be taken to back up Storage Gateway
    - All data is transmitted via SSL connections and is encrypted at rest with SSE
    - Data can be accesses via the Storage Gateway Service only
  - Gateway-Stored volumes
    - Allows data to be stored on-site and asynchronously backed to Amazon S3
    - Data and snapshots are backed up as EBS snapshots
    - Max size is 512 TB (32 volumes, 16 TB each)
    - All data is transferred with SSL and encrypted at rest with SEE
  - Gateway Virtual Tape Libraries (VTL)
    - Durable, cost effective solution to archiving data in the cloud
    - Allows for using existing tape infrastructure with virtual tapes
    - A tape gateway can have up to 1500 tables (1 PB of data)
    - When a tape it ejected, it is stored on a Virtual Tape Shelf in Glacier
    - One VTS per region

## Use Cases
- GC volumes enable expansion of local storage hardware to S3, increasing capacity without increasing hardware or storage processes
- GS volumes provide seamless secure backup of storage without new processes or hardware
- VTLs enable current tape backup software and processes while storing data more cost-effectively and simply on the cloud
