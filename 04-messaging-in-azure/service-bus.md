# Service Bus

- It is a fully managed, full blown message queueing service
- It is durable, chances of loosing messages are extremly slim
- Supports point-to-point (Queue) and pub/sub (Topic) scenarios
- Compativle with AMQP protocol (widely used for IoT devices) and JMS 2.0 API (Premium tier only)
- Advanced features:
    - Message sessions (guarantees FIFO)
    - Dead-letter queues
    - Scheduled delivery
    - Transactions
    - Duplicate detection

## Availability

- SLA: 99.9%
- Can be configured for geo-disaster recovery

## Security

- IP Firewall rules
- Service Endpoints
- Private Endpoints

## Service Bus Tiers

- Service Bus offers 3 tiers:
    - Basic:
        - Offers queues and scheduled messages
        - Message size: 256 KB
    - Standard:
        - Offers topics, transactions, de-duplication, sessions
        - Message size: 256 KB
    - Premium:
        - Aside of what standard tier offers, premium tier offers geo-disaster recovery (Geo-DR), resource isolations (increased security) and AZ support
        - Message size: 1 MB

## Pricing

- It is based on the selected tier and number of operations
