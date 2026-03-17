# Cosmos DB

- Fully managed NoSQL database
- Offers amazin performance: <10ms for 99% of operations
- It is globally distributed
- Offers fully automatic management for updates, scaling, fixes etc.
- Exposes multiple APIs: SQL, Mongo, Gremlin, Azure Table, Cassandra
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
- The API automatically picks the closest one
- When using write replication, the SLA is 99.999%
- For replicating data no code change is required, it is a configuration settings that can be enabled/disabled

## Backups

- CosmosDB offers full backups for every 1-24 hours (default: 4)
- Retention period for backups is 20-23 days (default is 30)

## Security

- IP firewall rules
- Service Endpoints
- Private Endpoints
- We can connect to Cosmos using Azure AD Authentication
- The communication is secured using TLS
- The data is encrypted by default

## Partitioning

- Data items in Cosmos are divided into partitions
- These are logical groups of items based on a specific property. For example, in case of a Cars databases, the model of the partitions can be created based on the model of the car (Mercedes, Chevrolet, Alfra Romeo, Suzuki, etc.)
- Partitions are the basic scale unit in CosmosDB
- Distribution and scale are per pertition
- When creating the partitions we would want to make sure that items are divided as evenly as possible
- It is extremly important to select the right partition property
- This cannot be modified after creation

## Consistency Levels

- Traditionally:
    - Relational databases have strong consistency: a call returns only after successfull commit in all replicas (High availability)
    - With no-sql databases we had eventual consistency: call returns immediatly, commit in replicas happens later (Low latency)
- With CosmosDB we have 5 consistency levels:
    - Strong: a transaction is commited only after data is replicated to other replicas (regions)
        - Region Y will get the last version of an item updated in region X
        - Recommended for mission critical data (data needs to be replicated immediately)
    - Bounded Staleness:
        - Region Y will lag behind region X by max K versions or by max T time
        - Keeps the order of the versions => the update order that region Y will see is the same as the updates happened in region X
        - Recommended for low write latency and when update orders are important
    - Session:
        - In a client session we have strong consistency
        - Other clients will use the Consisten Prefix consistency level
    - Consistent Prefix:
        - Keeps the order of the versions => the update order that region Y will see is the same as the updates happened in region X
        - There is no guarantee of the lag size (as opposed to Bounded Context)
        - Recommended for low write latency and when reads are infrequent
    - Eventual:
        - There is no order guarantee for the updates
        - There is no guarantee of the lag size (as opposed to Bounded Context)
        - Recommended to be used of uses cases sich as count of Re-Tweets, Likes, etc.
- CosmosDB consisntency levels are configured at the account level
- They can be relaxed on the request level

## CosmosDB Pricing

- It is based on RU/s - Request Unit per Second
- 1 RU = Read 1 item of size of 1 KB
    - Read = Get the item by its ID, not by query
    - Example: 400 R/U = Read 400 items of 1 KB in 1 second
- Update, delete, insert and querry operations cost more than 1 RU
- We can see the actual RU consumed in the response of the header of the result
- Pricing is also based on the following:
    - Operation type: Provisined, Auto Scale, Serverless
    - Write Regions
    - No. of provisioned RU/s
- Database operations:
    - Provisioned: 
        - Predefined number of RU/s, can be changed manually later
        - Offers reserved capacity up to 65% discount
    - Auto Scale:
        - We set the maximum RU/s, CosmosDB will scale up to this number if necessary
        - Good for unpredictable loads
    - Serverless:
        - We pay for what we use
        - Currently in preview, no SLA yet