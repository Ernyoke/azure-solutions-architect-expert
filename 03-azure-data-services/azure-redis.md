# Azure Redis

- It is managed Redis in Azure
- Redis is a fast in-memory, distributed cache. It is great for short-lived requently accessed data
- Azure Redis is fully compatible with OSS Redis (community edition) and Enterprise Redis (depending on the Azure service tier)

## Security

- IP firewall rules
- Service Endpoints
- Private Endpoints
- Secure communication used TLS
- There is no encrypted data in Redis, since nothing is stored on disk

## Azure Redis Service Tiers

- Basic:
    - Based on a single VM
    - No SLA
    - No distribution
    - Good for dev/test
- Standard:
    - Basd on two VMs, data is replicated
    - SLA: can be up to 99.9%
- Premium:
    - High-performance
    - Better trhoughput compared to Standard with lower latency
    - SLA: can be up to 99.95%
- Enterprise:
    - Based on Redis Enterprise
    - Offers additional features compared to Premium, such as RediSearch, RedisBloom and more
    - SLA: up to 99.99%
- Enterprise Flash:
    - Uses non-volatile memory to reduce storage cost
    - SLA: up to 99.99%

## Azure Redis Pricing

- It is based on the tier and instance (memory) selected
- Option for savings and reservation are available for Azure Redis