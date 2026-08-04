# Azure Logic Apps

- Managed workflow and integration platform for automating processes across Azure, on-premises systems and SaaS applications
- Best suited to connector-heavy orchestration, business processes and low-code integration
- Workflows are defined declaratively and can be created with the visual designer or as code
- A workflow begins with a **trigger** and continues through one or more **actions**

## Triggers, Actions and Connectors

- Triggers start a workflow, for example an HTTP request, schedule, Service Bus message or Event Grid event
- Actions perform work such as calling an API, transforming data, evaluating a condition or writing to a service
- **Managed connectors** provide hosted access to services such as Microsoft 365, Salesforce, SQL Server and SAP
- **Built-in connectors** run in the Logic Apps runtime and generally provide better performance in Standard workflows
- Use a **custom connector** to expose an API that has no suitable prebuilt connector
- Control-flow actions include conditions, loops, parallel branches, scopes and switch statements

## Hosting Models

| Requirement | Consumption | Standard |
| --- | --- | --- |
| Tenancy | Multi-tenant | Single-tenant |
| Workflows per Logic App resource | One | Multiple |
| Workflow types | Stateful | Stateful or stateless |
| Scaling | Automatic | Based on the selected hosting option |
| Cost model | Pay per trigger, action and connector execution | Pay for reserved hosting capacity |
| Networking and isolation | More limited | VNet integration, private endpoints and greater runtime control |
| Local development | Limited | Supported with Visual Studio Code |

- Choose **Consumption** for intermittent workflows that benefit from pay-per-execution pricing and do not require advanced network isolation
- Choose **Standard** for multiple related workflows, predictable performance, private networking, local development or stateless execution
- Standard can use the Workflow Service Plan or supported App Service Environment hosting for stronger isolation

## Stateful and Stateless Workflows

- **Stateful** workflows persist run history and intermediate state, making them suitable for long-running and durable processes
- **Stateless** workflows keep state in memory, favoring low latency and high throughput for short operations
- Stateless workflows have more limited run history and should not be used when durable checkpoints or long waits are required
- Long-running workflows can pause for timers, approvals or external callbacks without continuously consuming compute

## Reliability and Error Handling

- Configure retry policies for transient failures; common options include exponential, fixed, interval and no retry
- Group actions in **scopes** and use `runAfter` conditions to implement try/catch/finally-style handling
- Use timeouts and terminate actions so failed workflows do not wait indefinitely
- Enable concurrency control when parallel runs could update the same resource or violate ordering
- Design actions to be idempotent because triggers and connectors can deliver or retry work more than once
- Use tracked properties and correlation IDs to follow a business transaction across workflow steps

## Integration and Data Handling

- Use Service Bus for durable commands and enterprise messaging, Event Grid for discrete events and Event Hubs for high-volume streams
- Use the request trigger and response action to expose a workflow as an HTTP endpoint
- **Integration accounts** support business-to-business artifacts such as schemas, maps, trading partners and agreements
- Enterprise Integration Pack capabilities include XML validation/transformation and protocols such as AS2, X12 and EDIFACT
- Store large payloads externally and pass references when connector or message-size limits would be exceeded

## Security and Monitoring

- Prefer managed identities for access to Azure resources instead of credentials in workflow definitions
- Store secrets in Key Vault and restrict connector and resource permissions to least privilege
- Standard supports private endpoints for inbound access and VNet integration for reaching private dependencies
- Secure request triggers with Microsoft Entra ID, API Management, signed callback URLs or network controls as appropriate
- Azure Monitor and Log Analytics provide run metrics, diagnostics and alerting; run history exposes trigger and action inputs and outputs
- Apply secure input/output settings when run history could otherwise expose sensitive data

## Logic Apps versus Other Services

| Requirement | Recommended option |
| --- | --- |
| Visual workflow with many SaaS and enterprise connectors | Logic Apps |
| Short event-triggered custom code | Azure Functions |
| Stateful code-first orchestration | Durable Functions |
| Data movement and transformation pipelines | Azure Data Factory |
| API facade, policies and developer portal | API Management |
| Robotic automation of legacy user interfaces | Power Automate Desktop |

## Exam Design Decisions

- Integrate Microsoft 365, SAP or SaaS systems with minimal custom code -> **Logic Apps**
- Process a low-volume intermittent workflow at execution-based cost -> **Consumption**
- Host several workflows with private networking and predictable performance -> **Standard**
- Need a durable approval or external callback -> **stateful workflow**
- Need short, low-latency message processing without persisted state -> **stateless Standard workflow**
- Implement B2B schemas, maps and agreements -> **Logic Apps + integration account**
- Govern and publish a Logic Apps HTTP endpoint -> **API Management in front of Logic Apps**