# Azure Monitor

- It is a central location for all the monitoring aspects of Azure's resources
- Provides access to metrics, logs, insights and more
- Has additional capabilities not found in the individual resources

## What Azure Monitor Collects

- **Metrics**: numerical, time-series values; lightweight and near real-time; good for alerting and dashboards
- **Logs**: timestamped event/trace/performance records; stored in a Log Analytics Workspace; queried with KQL
- Data sources span the full stack:
    - **Application**: Application Insights (requests, dependencies, exceptions)
    - **Guest OS**: metrics and logs from inside VMs (via Azure Monitor Agent)
    - **Azure resource**: platform metrics and resource (diagnostic) logs
    - **Azure subscription**: Activity Log (control-plane operations, service health)
    - **Azure tenant**: Microsoft Entra ID logs (sign-ins, audit)
    - **Custom sources**: via Data Collection / ingestion APIs

## Core Building Blocks

- **Metrics database**: platform metrics auto-collected; retained ~93 days
- **Log Analytics Workspace**: central store for logs; queried with KQL; configurable retention/archive
- **Diagnostic settings**: route platform logs/metrics to Log Analytics, Storage, or Event Hub
- **Data Collection Rules (DCR)**: define what the Azure Monitor Agent collects and where it goes
- **Activity Log**: subscription-level audit of resource operations

## Capabilities Beyond Individual Resources

- **Cross-resource correlation**: query and alert across many resources from one place
- **Alerts & Action Groups**: metric, log, and activity log alerts with reusable notification/action groups
- **Insights**: curated experiences (Application Insights, VM Insights, Container Insights, Network Insights)
- **Visualization**: Workbooks, dashboards, and integration with Grafana / Power BI
- **Autoscale**: scale resources (e.g. VM Scale Sets, App Service) based on metric thresholds or schedules
- **Application Insights**: full APM - distributed tracing, live metrics, availability tests, dependency maps

## Azure Monitor vs Microsoft Sentinel

- **Azure Monitor**: operational monitoring, performance, and health of resources
- **Microsoft Sentinel**: cloud-native SIEM/SOAR built on top of Log Analytics; focused on security threat detection and response