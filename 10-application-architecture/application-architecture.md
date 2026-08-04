# Application Architecture Design

- Start with workload requirements: coupling, latency, throughput, ordering, delivery guarantees, failure handling, security and operational complexity
- Prefer synchronous communication only when the caller needs an immediate response
- Use asynchronous communication to isolate failures, absorb traffic spikes and scale producers independently from consumers
- Design every distributed operation for retries, duplicate delivery and partial failure

## Synchronous API Integration

- Use HTTP/REST or gRPC when the caller requires an immediate result
- Place Azure API Management in front of APIs when they need a governed facade, authentication, throttling, transformation, versioning or developer onboarding
- Use Application Gateway for regional Layer 7 routing to private backends and Front Door for global web/API routing
- Synchronous chains increase latency and failure coupling; avoid long sequences of service-to-service calls
- Apply timeouts, bounded retries, circuit breakers and bulkheads
- Use idempotency keys for operations that clients might retry

## Messaging and Event Selection

| Requirement | Recommended service |
| --- | --- |
| Enterprise commands, transactions, ordering, duplicate detection | Service Bus queue/topic |
| Simple low-cost queue with very large backlog | Storage Queue |
| React to discrete resource/business events | Event Grid |
| High-throughput telemetry or event streaming | Event Hubs |
| Stateful human/system workflow and connector orchestration | Logic Apps |
| Custom event processing code | Azure Functions |

### Commands versus Events

- A **command** asks a specific receiver to perform an action; use a queue when one consumer should process each message
- An **event** states that something happened; use publish/subscribe when multiple independent handlers may react
- Commands are commonly named imperatively (`CreateInvoice`); events are facts in past tense (`InvoiceCreated`)
- Do not use Event Grid as a durable work queue for long-running processing; hand work to Service Bus or another durable store when needed

## Delivery and Reliability

- Azure messaging generally provides **at-least-once delivery**, so consumers must be idempotent
- Use a unique operation/message identifier and store processing state to suppress harmful duplicates
- Send repeatedly failing messages to a dead-letter or poison-message location and monitor it
- Use exponential backoff with jitter for transient failures and cap retry attempts
- Preserve ordering only where the business requires it because ordering can reduce parallelism
- Service Bus sessions provide ordered processing for related messages
- Service Bus duplicate detection uses a configured history window and message ID
- Use the transactional outbox pattern when a database update and event publication must behave as one logical operation

## Common Patterns

| Pattern | Purpose |
| --- | --- |
| Queue-based load leveling | Buffer bursts and protect a constrained backend |
| Competing consumers | Process queue messages in parallel |
| Publish-subscribe | Deliver an event to multiple independent subscribers |
| Priority queue | Process urgent work before normal work |
| Claim check | Store a large payload externally and send only a reference |
| Choreography | Services react to events without a central coordinator |
| Orchestration | A workflow component coordinates a multi-step process |
| Saga | Coordinate a distributed transaction with compensating actions |

- Service Bus message size is limited; use the claim-check pattern with Blob Storage for large payloads
- Choreography reduces central coupling but can make end-to-end behavior harder to understand
- Logic Apps or Durable Functions can orchestrate long-running workflows with state, retries and compensation

## Event-Driven Architecture

- Event Grid routes discrete events to handlers and supports filtering, retry and dead-lettering
- Event Hubs ingests ordered streams through partitions; consumers track independent offsets through consumer groups
- Choose partition keys that preserve required per-entity ordering while distributing load evenly
- Event Hubs Capture writes streams to Blob Storage or Data Lake Storage for batch analytics and retention
- Events should be immutable, versioned and contain enough context or a stable reference for consumers
- Avoid exposing internal database schemas as public event contracts

## Caching

- Use Azure Managed Redis for distributed low-latency caching, session state, rate limiting and data structures when a managed Redis-compatible service is required
- **Cache-aside** is the common application pattern:
    1. Read from cache
    2. On a miss, read from the data store
    3. Populate the cache with an expiration
- Caching improves latency and reduces backend load but introduces stale-data and invalidation risks
- Select TTL based on how long stale data is acceptable; add jitter to avoid many keys expiring simultaneously
- Prevent cache stampedes with locking, request coalescing or proactive refresh
- Do not treat cache as the system of record unless the chosen architecture explicitly provides the required durability
- Front Door/CDN caching is for HTTP content near users; Redis caching is application/data caching near compute

## Workflow Selection

| Requirement | Recommended option |
| --- | --- |
| Many SaaS/enterprise connectors and visual workflow | Logic Apps |
| Short event-triggered custom code | Azure Functions |
| Stateful code-first orchestration | Durable Functions |
| Containerized background/event processing | Container Apps Jobs or event-driven Container Apps |
| High-volume stream processing | [Stream Analytics](../03-azure-data-services/azure-stream-analytics.md), Functions or another stream-processing engine |

- Logic Apps Consumption is multi-tenant and event-driven; Logic Apps Standard provides single-tenant hosting, multiple workflows per resource and additional networking/control
- Durable Functions provides orchestrator, activity and entity functions while handling state and replay
- Keep orchestrator code deterministic because it can replay from history

## Cross-Cutting Design

- Use correlation IDs and distributed tracing across API calls, messages and events
- Encrypt in transit, authenticate publishers/consumers and use managed identities where supported
- Apply least-privilege data-plane roles and private endpoints when public access is not allowed
- Define schema/version compatibility before independent teams deploy producers and consumers
- Monitor queue depth, message age, dead letters, processing latency, failures, cache hit ratio and dependency health
- Scale consumers based on backlog and processing time, not only CPU

## Exam Design Decisions

- Immediate request/response with API policies -> **API Management + synchronous API**
- Decouple a producer from one worker -> **queue**
- Multiple subscribers needing durable enterprise features -> **Service Bus topic**
- Notify handlers that an Azure resource changed -> **Event Grid**
- Millions of ordered telemetry events -> **Event Hubs**
- Buffer bursts before a constrained service -> **queue-based load leveling**
- Coordinate a long-running stateful code workflow -> **Durable Functions**
- Integrate SaaS systems with a visual workflow -> **Logic Apps**
- Shared low-latency application cache -> **Azure Managed Redis**
- Cache static HTTP content globally -> **Front Door/CDN**