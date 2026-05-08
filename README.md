# Azure Solutions Architect Expert (AZ-305) Study Notes

A comprehensive collection of study notes and documentation for the Microsoft Azure Solutions Architect Expert (AZ-305) certification exam.

## Overview

This repository contains structured notes covering Azure services, architectural concepts, and best practices required for the AZ-305 certification. The content is organized by topic areas with a focus on practical understanding and exam preparation.

## Table of Contents

### 00. Basic Concepts
- [Regions](./00-basic-concepts/regions.md) - Region selection based on proximity, service availability, availability zones, and pricing differences
- [Resource Groups](./00-basic-concepts/resource-group.md) - Logical containers for grouping resources by boundary and cost centers, naming conventions
- [Cost Management](./00-basic-concepts/cost.md) - Per-resource, consumption-based, and reservation pricing models; pricing calculator and budgets
- [SLA (Service Level Agreement)](./00-basic-concepts/sla.md) - Uptime percentages, combined SLA calculations, examples from 95% to 99.99%
- [Storage Accounts](./00-basic-concepts/storage-account.md) - Universal storage for databases, VM disks, diagnostics, and explicit data storage

### 01. Azure Compute
- [Compute Overview](./01-azure-compute/compute.md) - Four compute services (VMs, App Services, AKS, Azure Functions) with varying control levels
- [Virtual Machines](./01-azure-compute/vm.md) - VM sizing, cost optimization (autoshutdown, reserved/spot instances), availability sets and zones
- [App Service](./01-azure-compute/app-service.md) - Managed web hosting with deployment slots, autoscaling, and tiers from free to isolated
- [Azure Functions](./01-azure-compute/azure-functions.md) - Serverless event-driven functions with triggers, bindings, and cold start considerations
- [Azure Kubernetes Service (AKS)](./01-azure-compute/aks.md) - Managed Kubernetes for container orchestration, deployment, scaling, and high availability

### 02. Azure Networking
- [Virtual Networks (VNets)](./02-azure-networking/virtual-networks.md) - Private networks with subnets, NSGs, peering, service endpoints, and private link
- [Load Balancer](./02-azure-networking/load-balancer.md) - Layer 4 traffic distribution via 5-tuple hash; basic and standard SKUs with health probes
- [Application Gateway](./02-azure-networking/application-gateway.md) - Layer 7 load balancing with SSL termination, URL routing, WAF, and autoscaling

### 03. Azure Data Services
- [Databases on VMs](./03-azure-data-services/databases-on-vms.md) - IaaS approach with full control using prebuilt marketplace images; user manages SLA, updates, and security
- [Azure SQL](./03-azure-data-services/azure-sql.md) - Managed SQL Server with three flavors (SQL Database, Elastic Pool, Managed Instance), flexible pricing via DTU/vCore models
- [Azure MySQL](./03-azure-data-services/azure-mysql.md) - Managed MySQL with built-in security, tiered backups, and Basic/General Purpose/Memory Optimized pricing tiers
- [Azure PostgreSQL](./03-azure-data-services/azure-postgres.md) - Managed PostgreSQL with Hyperscale deployment, tiered backups with 7-35 day retention, and 99.99% SLA
- [Cosmos DB](./03-azure-data-services/cosmosdb.md) - Globally distributed NoSQL database with multiple APIs, five consistency levels, partition-based scaling, and RU/s pricing
- [Azure Redis](./03-azure-data-services/azure-redis.md) - Managed in-memory distributed cache; fully compatible with OSS/Enterprise Redis; five tiers (Basic, Standard, Premium, Enterprise, Enterprise Flash); TLS, IP firewall, Service/Private Endpoints; up to 99.99% SLA
- [Azure Storage](./03-azure-data-services/azure-storage.md) - Object store with Blobs, Files, Queues, Tables, and Disks; six redundancy options (LRS through RA-GZRS); Hot, Cool, and Archive access tiers with lifecycle rules; soft delete, container access levels, SAS tokens, private endpoints, and Front Door/CDN integration

### 04. Messaging in Azure
- [Event Grid](./04-messaging-in-azure/event-grid.md) - Event-based architectures with pub/sub model; no queues or ordering; up to 10M events/sec; 1 MB max event size; built-in HA with 99.99% SLA; pay per operation
- [Storage Queue](./04-messaging-in-azure/storage-queue.md) - Simple queue implementation within Azure Storage Account; 64 KB max message size; up to 20K messages/sec per account; same availability as blob storage
- [Event Hubs](./04-messaging-in-azure/event-hubs.md) - Big data streaming and event ingestion service (managed Kafka); millions of events/sec; partitions for ordering; consumer groups; up to 32 partitions per deployment; 99.95%-99.99% SLA; throughput units (TU) based pricing
- [Service Bus](./04-messaging-in-azures/service-bus.md) - Fully managed, durable message queueing service; supports point-to-point (Queue) and pub/sub (Topic); AMQP and JMS 2.0; advanced features (sessions/FIFO, dead-letter, scheduled delivery, transactions, duplicate detection); Basic/Standard/Premium tiers (256 KB to 1 MB messages); 99.9% SLA with geo-DR option

### 05. Identity Management
- [Microsoft Entra ID](./05-identity-management/entra-id.md) - Central identity and access management cloud service (formerly Azure AD); manages access to thousands of apps including the Azure Portal; tenants (directories) exist beside subscriptions and can be assigned to multiple subscriptions