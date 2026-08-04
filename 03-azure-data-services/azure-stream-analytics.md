# Azure Stream Analytics

- Azure Stream Analytics is a fully managed, real-time stream-processing service for analyzing high-volume event streams with low latency
- It uses a SQL-like query language with temporal, geospatial, pattern-matching and anomaly-detection functions
- Common scenarios include IoT telemetry, application logs, clickstream analysis, fleet tracking, real-time dashboards, alerts and predictive maintenance
- It is a processing engine, not an event broker or permanent data store; Event Hubs or IoT Hub commonly ingests the stream and an output service stores or acts on the results
- Jobs can run in Azure or on Azure IoT Edge when processing must occur close to devices

## Processing Pipeline

1. **Input** receives streaming events or reference data
2. **Query** filters, joins, aggregates or analyzes events continuously
3. **Output** sends results to storage, analytics, visualization, messaging or action services

### Inputs

- Streaming inputs include Azure Event Hubs, Azure IoT Hub and Blob Storage/Data Lake Storage
- A job can have multiple inputs
- Reference data is static or slowly changing data used to enrich a stream through joins
- Reference data can come from Blob Storage or Azure SQL Database
- Partitioned Event Hubs inputs allow queries to execute in parallel when the query preserves the partitioning scheme

### Outputs

- Common outputs include Event Hubs, Service Bus, Azure Functions, Blob Storage, Data Lake Storage, Azure SQL Database, Cosmos DB, Power BI and Azure Synapse Analytics
- A job can send the same processed stream to multiple outputs
- Choose the output based on the required action:
    - Power BI for real-time visualization
    - Azure Functions or Service Bus for downstream actions and workflows
    - Blob Storage or Data Lake Storage for retention and batch analytics
    - SQL Database or Cosmos DB for serving processed results

## Stream Analytics Query Language

- Queries use SQL-like `SELECT`, `FROM`, `WHERE`, `JOIN`, `GROUP BY` and `HAVING` syntax
- `TIMESTAMP BY` selects an event field as the event time; otherwise, processing uses the event's arrival time
- `System.Timestamp()` returns the timestamp assigned to the output event, such as the end of an aggregation window
- Stream-to-reference joins enrich events with lookup data
- Stream-to-stream joins must include a bounded time condition because both inputs are unbounded
- Built-in functions support temporal analysis, geospatial analysis, pattern matching and anomaly detection
- JavaScript and C# user-defined functions can extend query logic, but built-in functions are generally easier to scale and operate

## Window Functions

| Window | Behavior | Typical use |
| --- | --- | --- |
| Tumbling | Fixed-size, contiguous, nonoverlapping windows; each event belongs to one window | Totals per minute |
| Hopping | Fixed-size windows that advance by a hop; windows can overlap | Moving average emitted at regular intervals |
| Sliding | Emits when an event enters or leaves the time range | Detect a threshold over the most recent period |
| Session | Groups activity separated by less than an inactivity timeout, up to a maximum duration | User or device sessions |
| Snapshot | Groups events with the same timestamp | Aggregate simultaneous events |

- Windowed results are emitted at the end of the window
- Smaller or overlapping windows produce results more frequently but require more processing

## Event Time and Ordering

- Prefer event time when the source timestamp represents when the event actually occurred
- **Late-arrival tolerance** controls how long a job waits for delayed events
- **Out-of-order tolerance** controls how events with timestamps older than expected are adjusted or dropped
- Increasing tolerance can improve completeness but also increases latency and state requirements
- Ordering is only guaranteed within the relevant partition; choose partition keys based on the entity that requires ordered processing

## Scaling and Performance

- Stream Analytics capacity is measured in **Streaming Units (SUs)**, which represent allocated compute resources
- Increase SUs or use autoscaling when a job has sustained high utilization, increasing backlog or delayed output
- Partition inputs and make the query partition-compatible to scale processing horizontally
- Complex joins, large windows, high-cardinality aggregations and user-defined functions increase resource consumption
- Monitor the **watermark delay** to determine how far processing has fallen behind event time
- Stream Analytics clusters provide dedicated single-tenant capacity and virtual network support for larger or isolated workloads

## Reliability and Delivery

- The service maintains checkpoints so a job can recover its processing state after a failure
- Stream Analytics provides exactly-once event processing and at-least-once delivery; end-to-end exactly-once results depend on the selected output and query behavior
- Design outputs to tolerate duplicate writes when the sink does not support exactly-once delivery
- Availability-zone-enabled regions distribute job resources across zones automatically
- The service SLA is 99.9% availability at a minute-level granularity
- For regional disaster recovery, deploy equivalent jobs in another region and plan how inputs, outputs, checkpoints and failover are coordinated

## Security

- Use managed identities where supported instead of storing connection keys
- Apply least-privilege data-plane access to every input and output
- Data is encrypted in transit with TLS and built-in checkpoints are encrypted
- Use private endpoints for supported inputs and outputs when public access is prohibited
- Use a Stream Analytics cluster when the job itself requires virtual network integration

## Monitoring

- Monitor input events, output events, failed function requests, conversion errors, data errors, SU utilization and watermark delay
- A growing watermark delay or input backlog indicates that the job is not keeping up
- Configure Azure Monitor alerts and diagnostic logs for runtime errors, unavailable inputs and output failures
- Test queries with representative event volume, partitioning, late events and malformed data before production deployment

## Stream Analytics versus Related Services

| Requirement | Recommended service |
| --- | --- |
| Managed SQL-based real-time filtering, joins and windowed aggregation | Azure Stream Analytics |
| Durable ingestion, partitioned retention and replay | Event Hubs |
| Custom event-triggered code | Azure Functions |
| Large-scale Spark streaming or advanced custom processing | Azure Databricks |
| Event routing to discrete handlers | Event Grid |
| Enterprise commands, queues and topics | Service Bus |

## Exam Design Decisions

- Real-time SQL aggregation over Event Hubs telemetry -> **Stream Analytics**
- Enrich a live stream with static lookup data -> **reference data input + join**
- Count events in separate five-minute intervals -> **tumbling window**
- Calculate a ten-minute average every minute -> **hopping window**
- Group user activity until an inactivity gap -> **session window**
- Preserve the source occurrence time -> **`TIMESTAMP BY` event-time field**
- Scale a partitioned stream in parallel -> **align input partition keys and query partitioning**
- Retain and replay raw events without transforming them -> **Event Hubs**, not Stream Analytics
- Run complex custom Spark-based stream processing -> **Azure Databricks**