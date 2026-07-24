# Cosmos DB

- Fully managed NoSQL database (also offers a distributed relational API: PostgreSQL)
- Offers amazing performance: <10 ms latency at the 99th percentile for point reads and writes (1 KB items)
- It is globally distributed (turnkey replication to any Azure region)
- Offers fully automatic management for updates, scaling, fixes etc.
- Schema-agnostic: all item properties are automatically indexed by default (indexing policy is configurable)
- Exposes multiple APIs:
    - **NoSQL** (formerly SQL API) - native API, gets new features first, recommended for new apps
    - **MongoDB** - for migrating/reusing MongoDB apps (RU-based or vCore)
    - **Cassandra** - wide-column, CQL
    - **Gremlin** - graph data
    - **Table** - drop-in replacement for Azure Table Storage
    - **PostgreSQL** - distributed relational (Citus), for scale-out PostgreSQL
- CosmosDB is a hierarchical database:
    - Database Account -> Databases -> Containers -> Items (JSON documents)
    - Containers can also have:
        - Stored procedures
        - User-defined functions
        - Merge procedures
        - Triggers
        - Conflicts

## Availability

- CosmosDB can be distributed across many regions (configurable)
- The API automatically picks the closest region
- SLA:
    - 99.99% read/write for single-region accounts
    - 99.999% read availability for multi-region accounts (single write region)
    - 99.999% read and write when multi-region writes are enabled
- For replicating data no code change is required, it is a configuration setting that can be enabled/disabled
- **Availability Zones** can be enabled per region for zone redundancy
- **Service-managed failover** automatically promotes a read region if the write region goes down (manual failover can also be triggered for testing)
- With **multi-region writes** every region accepts writes => conflicts are possible and are resolved by:
    - Last Write Wins (LWW) on a timestamp/numeric property - default
    - Custom conflict resolution policy (stored procedure) or a conflicts feed handled by the app

## Backups

- **Periodic backup** (default): backup interval 1-24 hours (default 4 hours), retention 8 hours - 30 days (default 8 hours)
    - 2 backup copies are stored free of charge, additional copies are billed
    - Restore requires a support request and always restores into a **new account**
- **Continuous backup (point-in-time restore)**: 7-day or 30-day retention, self-service restore to any second within the window
- Backups are taken automatically without affecting performance or consuming RU/s

## Security

- IP firewall rules
- Service Endpoints
- Private Endpoints (Private Link) + disable public network access
- We can connect to Cosmos using Microsoft Entra ID authentication (with RBAC and managed identities) instead of account keys
    - Control plane RBAC (Azure RBAC roles) vs data plane RBAC (Cosmos DB built-in data roles)
    - Keys can be rotated; read-only keys and resource tokens allow limited access
- The communication is secured using TLS
- The data is encrypted at rest by default (service-managed keys, optionally customer-managed keys in Key Vault)
- Diagnostic logs and auditing can be sent to Azure Monitor / Log Analytics

## Partitioning

- Data items in Cosmos are divided into partitions
- These are logical groups of items based on a specific property. For example, in case of a Cars database, the partitions can be created based on the model of the car (Mercedes, Chevrolet, Alfa Romeo, Suzuki, etc.)
- Partitions are the basic scale unit in CosmosDB
- Distribution and scale are per partition
- When creating the partitions we would want to make sure that both data and requests are divided as evenly as possible (avoid "hot partitions")
- It is extremely important to select the right partition key
- The partition key cannot be modified after container creation (a new container + data migration is needed)
- Limits:
    - A logical partition (one partition key value) can hold max **20 GB** of data
    - A physical partition can serve max **10,000 RU/s**
- Good partition key: high cardinality, evenly spread reads/writes, frequently used in query filters
- If no single property fits, we can use a **synthetic partition key** (concatenated properties) or **hierarchical (sub) partition keys**
- Transactions (batches, stored procedures, triggers) are scoped to a single logical partition

## Consistency Levels

- Traditionally:
    - Relational databases have strong consistency: a call returns only after a successful commit in all replicas (high consistency, higher latency)
    - With no-sql databases we had eventual consistency: call returns immediately, commit in replicas happens later (low latency)
- With CosmosDB we have 5 consistency levels (from strongest to weakest):
    - Strong: a transaction is committed only after data is replicated to the other replicas (regions)
        - Region Y will get the last version of an item updated in region X
        - Recommended for mission critical data (data needs to be replicated immediately)
        - Highest read RU cost and highest write latency; not supported with multi-region writes
    - Bounded Staleness:
        - Region Y will lag behind region X by max K versions or by max T time
        - Keeps the order of the versions => the update order that region Y will see is the same as the updates happened in region X
        - Recommended when we need near-strong consistency with better latency and a predictable staleness window
    - Session (default):
        - In a client session we have strong consistency (read-your-own-writes, monotonic reads/writes)
        - Other clients will use the Consistent Prefix consistency level
        - Most commonly used level, good balance of consistency, availability and performance
    - Consistent Prefix:
        - Keeps the order of the versions => the update order that region Y will see is the same as the updates happened in region X
        - There is no guarantee of the lag size (as opposed to Bounded Staleness)
        - Recommended for low write latency and when reads are infrequent
    - Eventual:
        - There is no order guarantee for the updates
        - There is no guarantee of the lag size (as opposed to Bounded Staleness)
        - Lowest latency and lowest read RU cost
        - Recommended for use cases such as count of Re-Tweets, Likes, etc.
- Trade-off: the stronger the consistency, the higher the read RU cost and write latency, and the lower the availability
- CosmosDB consistency levels are configured at the account level
- They can be relaxed on the request level (they can never be strengthened above the account level)

## CosmosDB Pricing

- It is based on RU/s - Request Unit per Second
- 1 RU = Read 1 item of size of 1 KB
    - Read = Get the item by its ID, not by query
    - Example: 400 R/U = Read 400 items of 1 KB in 1 second
- Update, delete, insert and query operations cost more than 1 RU
- We can see the actual RU consumed in the `x-ms-request-charge` response header
- Exceeding the provisioned RU/s results in throttling (HTTP 429 - "request rate too large"), which the SDKs retry automatically
- Pricing is also based on the following:
    - Throughput mode: Provisioned, Auto Scale, Serverless
    - Write Regions (each additional region multiplies the throughput cost)
    - No. of provisioned RU/s
    - Consumed storage (data + indexes) and backup copies
- Throughput can be provisioned at **database level** (shared by the containers) or at **container level** (dedicated)
- Database operations:
    - Provisioned (manual):
        - Predefined number of RU/s (min 400 RU/s per container), can be changed manually later
        - Offers reserved capacity up to 65% discount
        - Best for predictable, steady workloads
    - Auto Scale:
        - We set the maximum RU/s, CosmosDB will scale automatically between 10% and 100% of that value
        - ~1.5x the price per RU, but we only pay for the highest RU/s used in each hour
        - Good for unpredictable or spiky loads
    - Serverless:
        - We pay for what we use (per consumed RU), no minimum throughput
        - Good for dev/test and small, intermittent workloads
        - Limitations: single region, no autoscale, lower storage/throughput limits
- **Free tier**: first 1000 RU/s and 25 GB of storage are free (one free tier account per subscription)

## Other Exam-Relevant Features

- **Time to Live (TTL)**: automatic expiration and deletion of items, set at container or item level (no RU cost for the deletes)
- **Change Feed**: persistent, ordered record of changes (inserts and updates) in a container
    - Consumed via the change feed processor or an Azure Function with the Cosmos DB trigger
    - Typical uses: event sourcing, materialized views, replication to other stores, real-time stream processing
- **Azure Synapse Link**: HTAP - a column-oriented analytical store over the container that Synapse can query with no ETL and no impact on transactional performance/RUs
- **Integrated cache** (dedicated gateway): reduces RU cost and latency for repeated point reads and queries
- **Global read scale-out**: add read regions and let the SDK use preferred regions to serve reads locally
- **Migration tooling**: Azure Data Migration Service, Azure Data Factory, Spark connector, native per-API tools (e.g. mongorestore)
- **Monitoring**: Azure Monitor metrics (RU consumption, throttled requests, storage per partition), diagnostic logs, Cosmos DB Insights
- When to choose Cosmos DB: globally distributed, low-latency, high-throughput, schema-flexible workloads (IoT, retail catalogs, gaming, personalization, real-time telemetry)
- When not to choose it: complex joins/reporting, heavy relational workloads or cross-partition ACID transactions => use Azure SQL or Synapse instead