# ElastiCache

## Key points
- How to improve application performance using caching
- How to launch cache environments in the cloud
- How to launch cache environments in the cloud
-  What are the basic differences and use cases for Memcached and Redis?
- How to scale your cluster vertically
- How to scale your Memcached cluster horizontally using additional cache nodes
- How to scale your Redis cluster horizontally using replication groups
- How to back up and recover your Redis cluster
- How to apply a layered security model

## In-Memory Caching
- Compared to retrieving data from an in-memory cache, querying a database is an expensive operation
- Two major Cache engines
  - Memcached
    - Simple to use in-memory key/value store that can be used to store arbitrary types of data
  - Redis
    - Flexible in-memory data structure store that can be used as a cache, database, or message broker

## Amazon ElastiCache
- Web service that simplifies the setup and management of distributed in-memory caching environments
- This makes it easy and cost effective to provide a high-performance and scalable caching solution for cloud applications
- Only the endpoint in configuration files needs to be changed to switch between Memcached and Redis
- With Redis, ElastiCache
makes it easy to set up read replicas and fail over from the primary to a replica in the event of a problem.

### Data Access Patterns
- Retrieving a flat key from an in-memory cache will always be faster than the most optimized database query
- Thought should be placed beforehand to determine what should and should not be cached

### Cache Engines
- Memcached
  - Very simple interface that allows for writing and reading objects into in-memory key/value data stores.  
  - With ElastiCache, the cluster can be elastically grown or shrunk to meet needs
  - Clusters can be partitioned into shards and have operations parallelized for very high throughput
  - Memcached deals with objects that are blobs, they can be retried with a unique key
  - Many common versions are supported
- Redis
  - Supports objects, strings, lists, and sets
  - Unlike Memcached, in-memory data can be stored to disk, allowing for snapshots to be created
  - Clusters can support up to 5 read replicas in order to offload read requests
  - A read replica can become the primary node in the event of a failure
  - Redis can also sort and rank data, useful as a message broker

### Nodes and Clusters
- A single Memcached cluaster can contain up to 20 nodes
- Every Redis node is in it's own cluster, but clusters can form replication groups
- A few large nodes or many smaller nodes are possible
- If a node fails, a replacement will be deployed and added back to the cluster

### Memcached Auto Discovery
- Auto Discovery simplifies application code by no longer needing awareness of the infrastructure topology of the cache cluster in the application layer

### Scaling
- Horizontal
  - Memcached
    - Partitioning data, scaling to 20+ nodes
    - Auto Discovery allows memached nodes to be added or removed
  - Redis
    - Single node that is handling read and write transactions
    - Up to 5 nodes can be handling read requests
- Vertical
  - Clusters can not be resized vertically, however a new cluster can be spun up quickly allowing for vertical scaling with a new instance
  - New Memcached clusters start empty, while Redis clusters can be restored with a backup

### Replication and Multi-AZ
- Replication is useful for rapid recovery in the event of a node failure
- Redis offers data protection, Memcached does not
- A replication group consists of up to 6 clusters, 5 designed as read replicas

### Multi-AZ Replication Groups
- Automates the failover and replacement of the primary node
- A new node will replace the failed one after an existing read replica will be promoted to the primary node
- Elasticache will update the DNS entry, so apps can find the new node

### Backup and Recovery
- Redis clusters can create snapshots, Memcached can not create snapshots because it is always in-memory
- It is best to perform a snapshot against one of the read replicas instead of the primary node
- Manual and Automated snapshots can be used

### Access Control
- Access is primarily controlled by restricting inbound network access to the cluster, this is accomplished through security groups
- Additional security can be restricted at the subnet level with accomplished
- Access to the infrastructure and endpoints can be defined separately
