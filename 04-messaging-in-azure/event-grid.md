# Event Grid

- Allows building event-based architectures
- Used to publish events to interested parties
- With Event Grid we have no queues => no order
- It has strong integration with many other Azure services
- It is cost effective, has the simplest pricing model
- Has no tiers, HA is built-in by default

## Event Grid Terminology

- **Event**: defines what happened, for example a storage blobb was added, IOT telemetry received
- **Publisher**: defines who created the event. Example: Microsot, my organization, etc.
- **Event Source**: defines where the event happened, eg. storage account, IOT Hub
- **Topic**: defines where the event is sent, used to group related events
- **Subscription**: which events interests us
- **Event Handler**: defines where the event is sent, eg. Azure Functions, Event Hubs, custom code, etc.

## Event Grid SLA

- Event Grid has a built-in SLA of 99.99%
- Max event size is 1 MB (larget then Storage Queue)

## Performance

- Up to 10 million events / sec
- 5000 events /sec / topic
- Latency: subsecond end-to-end latency in the 99th percentile

## Event Gird Pricing

- Pricing in based on number of opertaions, first 100K operations are free per month