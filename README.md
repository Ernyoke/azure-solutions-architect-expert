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