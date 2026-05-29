# Resource Monitoring

- Almost every resource in Azure has a "Monitoring" section, with items such as:
    - Insights: various insights about the resource
    - Alerts: alerts to be triggered when something happens, eg. CPU utilization goes above a specific value
    - Metrics
    - Diagnostic settings
    - Logs

## Metrics

- Collected automatically at regular intervals for most resources (platform metrics) - no configuration required
- Retained for **93 days** by default in the metrics store
- To keep metrics longer, route them to a Log Analytics Workspace, Storage Account, or Event Hub via Diagnostic settings
- **Metrics Explorer**: visualize, filter, split and pin metric charts to dashboards
- **Multi-dimensional metrics**: metrics can have dimensions (key-value pairs) to filter and split (e.g. by instance, status code)

## Diagnostic Settings

- Control how platform logs and metrics are exported from a resource
- Define **what** to collect (log categories, metrics) and **where** to send it
- Up to 5 diagnostic settings per resource
- Destinations:
    - **Log Analytics Workspace**: query and analyze with KQL, correlate across resources
    - **Storage Account**: cheap long-term archival / audit retention
    - **Event Hub**: stream to third-party / SIEM tools (e.g. Splunk, Datadog) or custom pipelines
    - **Partner solutions**

## Alerts

- With alerts we can get notifications about resources in our Azure subscription
- Examples:
    - VM's CPU goes above 90%
    - More than 20% of requests fail
    - Server error occurs in the last hour

### Alert Components

- Components are:
    - Conditions: when to trigger the alert
    - Actions: what to do when the alert is triggered. Usually when an alert is triggered, we send a notification. Notifications are sent to Action Groups
    - Details: Contents of the notification, what information to be included in the alert

### Alert Types (signals)

- **Metric alerts**: evaluate resource metrics at regular intervals; near real-time; support static and dynamic thresholds (ML-based)
- **Log (search) alerts**: run a saved KQL query against a Log Analytics Workspace on a schedule; more flexible but higher latency
- **Activity Log alerts**: fire on subscription-level events (resource created/deleted, service health, security events)
- **Smart Detection** (Application Insights): automatically detects anomalies like failure-rate spikes

### Alert Severity

- Sev 0 = Critical, Sev 1 = Error, Sev 2 = Warning, Sev 3 = Informational, Sev 4 = Verbose

### Action Groups

- A reusable collection of notification preferences and actions, triggered by alerts
- **Notifications**: Email, SMS, push (Azure mobile app), voice
- **Actions**: Automation Runbook, Azure Function, Logic App, Webhook, ITSM connector, Event Hub
- Can be reused across many alert rules

### Alert Processing Rules

- Modify alerts as they fire without changing the alert rules
- Use cases: suppress notifications during planned maintenance windows, or apply an action group at scale across a scope

### Alert Pricing

- Per metrics measured: $ 0.1 / metric / month
- Per notification type:
    - First 1k email, 1k push notification, 100k web hooks, 100 SMS in the US are included for free for each month
- All in all - quite cost effective

## Logs

- Almost every Azure resource generates logs
- Logs might contain data about performance, events, errors, etc.
- Log records need a central repository to be stored and viewed => this is the Log Analytics Workspace

### Log Analytics Workspace

- It is a centralized location for storing, organizing and analyzing logs
- It aggregates logs from all connected, monitored resources
- It uses specialized query language to query logs (Kusto)
- It is located in a designated region => recommended to be in the same region as the resources to save costs
- **Retention**: default 30 days (free); configurable up to 730 days (2 years); archive tier up to 7 years for low-cost long-term storage
- **Data ingestion** via agents, Diagnostic settings, Application Insights, and custom APIs
- **Tables**: data is stored in typed tables (e.g. `Heartbeat`, `AzureActivity`, `Perf`, `AppRequests`)
- Design choice: centralized (single workspace, easier management/correlation) vs decentralized (workspace per team/region, data sovereignty & access control)

### KQL (Kusto Query Language)

- Read-only query language used to query Log Analytics, Application Insights, and Azure Data Explorer
- Common operators: `where`, `summarize`, `project`, `extend`, `join`, `order by`, `top`, `render`
- Example: `Perf | where CounterName == "% Processor Time" | summarize avg(CounterValue) by bin(TimeGenerated, 5m)`

## Monitoring Agents

- **Azure Monitor Agent (AMA)**: current unified agent for VMs and servers; collects guest OS metrics and logs; configured via **Data Collection Rules (DCR)**; replaces legacy agents
- **Legacy (being retired)**: Log Analytics agent (MMA/OMS), Telegraf, Diagnostics extension (WAD/LAD)
- **Data Collection Rules (DCR)**: define what to collect and where to send it for AMA; reusable across machines

## Insights

- A collection of metrics, statistics and insights about resources
- Insights are specific to resource types and they are generated automatically
- Code-based services (App-Services, apps on VMs) can integrate Application Insights into the code and gain a lot of data about app usage, performance, etc.
- Curated experiences for specific scenarios:
    - **Application Insights**: APM for web apps - request rates, response times, failures, dependencies, distributed tracing, live metrics, availability tests
    - **VM Insights**: performance and dependency map for VMs and scale sets
    - **Container Insights**: monitoring for AKS clusters, nodes, and pods
    - **Network Insights**, **Storage Insights**, etc.

## Visualization

- **Workbooks**: interactive, customizable reports combining metrics, logs (KQL), and text
- **Dashboards**: pin charts and query results to Azure portal dashboards
- **Azure Monitor integration with Grafana / Power BI** for advanced visualization

## Service Health & Resource Health

- **Azure Service Health**: notifications about Azure service issues, planned maintenance, and health advisories affecting your subscription
- **Resource Health**: current and historical health status of an individual resource