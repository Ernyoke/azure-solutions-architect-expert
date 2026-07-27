# Service Bus

- It is a fully managed, full blown message queueing service
- It is durable, chances of loosing messages are extremly slim
- Supports point-to-point (Queue) and pub/sub (Topic) scenarios
- Compativle with AMQP protocol (widely used for IoT devices) and JMS 2.0 API (Premium tier only)
- Also supports HTTPS/REST and AMQP over WebSockets (port 443) when port 5671 is blocked
- Broker style messaging: messages are **pulled** by the receiver (unlike Event Grid, which pushes)
- Advanced features:
    - Message sessions (guarantees FIFO)
    - Dead-letter queues
    - Scheduled delivery
    - Transactions
    - Duplicate detection

## Core Concepts

- **Namespace**: top level container for all messaging entities, gets a unique FQDN `<name>.servicebus.windows.net`. Tier, capacity, network rules and Geo-DR are configured at namespace level
- **Queue**: point-to-point, a message is consumed by one receiver (competing consumers), messages are stored until processed
- **Topic + Subscription**: pub/sub, each subscription gets its own copy of the message; a subscription behaves like a virtual queue
- **Subscription rules / filters** (how a subscription selects messages):
    - **Boolean filters**: TrueFilter (receives everything, default) / FalseFilter
    - **SQL filters**: SQL-like expression over system and custom properties - most flexible, most expensive
    - **Correlation filters**: match on `CorrelationId` and other system properties - more efficient than SQL filters
    - **SQL actions** can modify message properties when a rule matches
- **Message**: binary payload (body) + system properties + custom application properties (used for filtering and routing)

## Message Handling

- **Receive modes**:
    - **Peek-Lock** (default, at-least-once): message is locked while the receiver completes / abandons / dead-letters / defers it
    - **Receive-and-Delete** (at-most-once): message is removed immediately, message loss possible if the consumer crashes
- **Lock duration**: default 30 seconds, max 5 minutes, can be renewed by the client
- **Max delivery count**: default 10, afterwards the message is moved to the dead-letter queue
- **Dead-letter queue (DLQ)**: sub-queue for messages that cannot be processed (delivery count exceeded, TTL expired, filter evaluation error, explicit dead-lettering)
- **Time to live (TTL)**: per-message or entity default; max 14 days on Basic/Standard, unlimited on Premium
- **Duplicate detection**: based on `MessageId` within a time window (default 30 seconds, up to 7 days). Standard/Premium only and must be enabled at entity creation
- **Sessions**: messages sharing a `SessionId` go to a single receiver in FIFO order, also enable session state. Required for ordered processing and request/response patterns
- **Scheduled delivery**: message becomes visible at a future point in time (can be cancelled)
- **Deferral**: consumer postpones a message and retrieves it later by sequence number
- **Transactions**: group operations (e.g. send + complete) into one atomic unit, within a single namespace ("send via" transfer queues)
- **Batching**: client-side batching and batch send/receive for higher throughput
- **Auto-forwarding**: chain an entity to another queue/topic in the same namespace (e.g. subscription -> queue)
- **Auto-delete on idle**: automatically delete an entity after an idle interval (min 5 minutes)

## Availability

- SLA: 99.9%
- Premium supports **Availability Zones** (zone redundancy within a region)
- Can be configured for geo-disaster recovery
    - **Geo-DR (Premium)**: pairs a primary and secondary namespace behind an **alias**, replicates **metadata only** (entities, not messages). Failover is manual and one-way, pairing must be re-created afterwards
    - **Geo-replication (Premium)**: replicates metadata **and** message data to a secondary region
- **Partitioning** (Basic/Standard) spreads an entity across multiple message brokers for more throughput and availability, must be set at creation time

## Security

- IP Firewall rules
- Service Endpoints
- Private Endpoints (Premium tier only)
- **Microsoft Entra ID** authentication with managed identities (recommended). Built-in roles:
    - Azure Service Bus Data Owner
    - Azure Service Bus Data Sender
    - Azure Service Bus Data Receiver
- **SAS** policies with Send / Listen / Manage rights, scoped to the namespace or a single entity
- Local (SAS) authentication can be disabled to enforce Entra ID only
- Encrypted at rest by default, **customer-managed keys (CMK)** in Key Vault supported on Premium

## Service Bus Tiers

- Service Bus offers 3 tiers:
    - Basic:
        - Offers queues and scheduled messages
        - No topics, sessions, transactions or duplicate detection
        - Message size: 256 KB
    - Standard:
        - Offers topics, transactions, de-duplication, sessions
        - Shared multi-tenant capacity, variable throughput and latency
        - Message size: 256 KB
    - Premium:
        - Aside of what standard tier offers, premium tier offers geo-disaster recovery (Geo-DR), resource isolations (increased security) and AZ support
        - Dedicated resources via **messaging units (MU)**: 1, 2, 4, 8, 16 - predictable performance, can be scaled up/down
        - Only tier with JMS 2.0, Private Endpoints, CMK, AZ support, Geo-DR / Geo-replication and larger entities
        - Message size: 1 MB (up to 100 MB with large message support)

## Limits (exam relevant)

- Max queue / topic size: 1-5 GB on Basic/Standard, up to 80 GB on Premium
- Max subscriptions per topic: 2,000
- Max SQL filters per topic: 2,000, correlation filters: 100,000
- Max concurrent connections: 1,000 (Basic/Standard, AMQP)

## Pricing

- It is based on the selected tier and number of operations
- Basic/Standard: pay per operation (Standard adds a base charge and brokered connection charges)
- Premium: pay per **messaging unit per hour**, operations included - predictable cost

## When to Choose Service Bus

- Enterprise messaging needing ordering (sessions/FIFO), transactions, duplicate detection or dead-lettering
- Decoupling components, load leveling, request/response over messaging
- Choose **Storage Queue** instead for simple queueing, >80 GB of messages, lowest cost and no advanced features
- Choose **Event Grid** for reactive event distribution (push, near real-time) and **Event Hubs** for high-throughput telemetry/streaming ingestion
