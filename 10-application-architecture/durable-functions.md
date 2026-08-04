# Azure Durable Functions

- Extension of Azure Functions for building stateful, long-running workflows in code
- The Durable Task framework persists execution history, checkpoints and state so workflows can survive restarts and scale-out
- Developers describe coordination in code while the extension handles state persistence, retries, timers and recovery
- Best suited when orchestration requires custom application logic and a code-first development model

## Function Types

- **Client functions** start, query, suspend, resume, terminate or raise events to an orchestration instance
- **Orchestrator functions** define workflow order and coordinate activities, sub-orchestrations, timers and external events
- **Activity functions** perform the actual work, including I/O and calls to external systems
- **Entity functions** manage small pieces of durable state through serialized operations
- Each orchestration instance has a unique instance ID that clients use for management and status queries

## Orchestration Patterns

| Pattern | Purpose |
| --- | --- |
| Function chaining | Run activities in a sequence and pass each result to the next step |
| Fan-out/fan-in | Run independent activities in parallel and aggregate their results |
| Async HTTP API | Start long-running work and expose status endpoints for polling |
| Monitor | Check a condition periodically with durable timers |
| Human interaction | Wait for an external event, usually with a timeout |
| Aggregator | Collect event data into a durable entity over time |

- Use sub-orchestrations to split a large workflow into reusable or independently managed sections
- Use durable timers rather than blocking threads or calling ordinary sleep APIs
- External events allow an orchestration to wait for approvals, callbacks or signals from another system

## Replay and Determinism

- Orchestrators rebuild local state by replaying their persisted event history
- Orchestrator code must be **deterministic**: the same history must produce the same decisions each time it replays
- Do not perform direct network, database or file I/O in an orchestrator; place that work in activity functions
- Use the orchestration context APIs for current time, durable timers and deterministic identifiers
- Avoid random values, environment-dependent decisions and non-deterministic concurrency inside orchestrators
- Replay can emit duplicate log entries unless replay-safe logging is used

## Reliability and Instance Management

- Configure automatic retry policies for activities and sub-orchestrations that can fail transiently
- Activity functions may execute more than once after failures, so they must be idempotent where duplicate effects are harmful
- Orchestration state is durable, but a workflow is not an atomic transaction across external systems
- Use compensating activities or a saga when completed steps must be undone after a later failure
- Clients can query status and history, send events, or terminate an instance through management APIs
- Use `ContinueAsNew` for eternal or very long-running orchestrations to replace history and control its growth
- Use orchestration versioning strategies when deployments change in-flight workflow logic

## Task Hubs and Storage

- A **task hub** is the logical set of storage resources used by Durable Functions to coordinate instances
- Function apps sharing a storage account should use unique task hub names to prevent accidental interference
- The default Azure Storage provider uses queues, tables and blobs to persist messages, history and state
- Storage account performance and throttling can limit orchestration throughput; monitor storage latency and request rates
- Keep the Function App and backing storage in the same region and secure access with managed identity where supported

## Scaling, Cost and Monitoring

- Durable Functions uses the Functions hosting plan and scales based on pending orchestration and activity work
- Large histories, excessive fan-out and frequent checkpoints increase storage operations and replay cost
- Bound fan-out when downstream systems or storage cannot safely absorb unlimited parallelism
- Application Insights can correlate orchestration and activity telemetry, but replay behavior must be considered when interpreting logs
- Monitor failed and pending instances, activity latency, storage throttling and poison messages

## Durable Functions versus Other Services

| Requirement | Recommended option |
| --- | --- |
| Stateful workflow written and tested as application code | Durable Functions |
| Visual workflow and broad connector catalog | Logic Apps |
| Single short stateless operation | Azure Functions |
| Durable messaging without workflow coordination | Service Bus |
| Containerized event processor or scheduled job | Container Apps Jobs |

- Logic Apps emphasizes declarative design and managed connectors; Durable Functions emphasizes code, custom logic and developer control
- Durable Functions coordinates work but does not replace a message broker when independent producers and consumers need durable messaging

## Exam Design Decisions

- Run dependent functions in sequence -> **function chaining**
- Execute many independent tasks and combine the results -> **fan-out/fan-in**
- Return immediately for long-running HTTP work and provide status polling -> **async HTTP API**
- Wait durably for an approval with an expiration -> **external event + durable timer**
- Maintain addressable state with serialized operations -> **durable entity**
- Prevent non-deterministic replay behavior -> **move I/O to activities and use orchestration context APIs**
- Prevent unbounded history in a recurring orchestration -> **ContinueAsNew**
- Undo completed distributed steps after a later failure -> **compensating activities/saga**