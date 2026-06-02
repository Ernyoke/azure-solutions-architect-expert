# Azure Functions

- Azure Functions are small, focused functions running as a result of an event => great for event-driven systems
- Azure Functions are automatically managed by Azure => Azure can start, stop and autoscale them
- Azure Functions have flexible pricing plans
- They are **serverless** (we don't manage the underlying infrastructure)
- Common use cases: event-driven processing, scheduled tasks, lightweight APIs, glue/integration logic between services
- A Function App is the deployment and management unit => it hosts one or more individual functions that share the same hosting plan, configuration and runtime
- Each Function App requires an associated **Azure Storage Account** (used for triggers, logging, and state management)

## Triggers and Bindings

- Triggers:
    - They are the events that make the functions run
    - Triggers are deeply integrate into other Azure services
    - Technically, triggers are not mandatory, we can create Azure Functions without triggers
    - Supported trigger types in Azure:
        - Blob storage
        - CosmosDB
        - Dapr
        - Event Grid
        - Event Hubs
        - HTTP requests
        - IOT Hubs
        - Kafka
        - Queue storage
        - RabbitMQ
        - Service Bus
        - Timer
- Bindings:
    - They are declarative connections to other resources
    - With a binding we can set up a connection between a function and another resource
    - Bindings can be input, output or both
    - Bindings are provided as input to the functions
    - Bindings are also not mandatory
    - Binding types (input or output):
        - Blob Storage
        - Cosmos DB
        - Dapr
        - Event Grid
        - Event Hub
        - HTTP request
        - IOT Hub
        - Kafka
        - Mobile Apps
        - Notification Hub
        - Queue Storage
        - RabbitMQ
        - SendGrid
        - Service Bus
        - SignalR
        - Table Storage

## Supported programming languages for Azure Functions

- List includes:
    - C#/F#
    - JavaScript (NodeJs)
    - Java
    - Python
    - Powershell
    - TypeScript
- This list is growing as other languages are supported in the future
- Languages can also be supported through **custom handlers** (a lightweight web server in any language)

## Cold Starts

- Azure Functions are completely managed by Azure => after some time of inactivity Azure might take down the function's host
- The next activation of the function will take time (2-3 seconds before the code runs)
- How to avoid cold starts?
    - Select the right hosting plan

## Hosting Plans

- Hosting plans are:
    - Consumption (legacy):
        - We pay only for what we actually use: execution time (GB/sec) + total number of executions;
        - Consumption plan has a max limit of 1.5 GB of RAM
        - Price calculation example:
            - Execution per month: 9 million
            - Avg. memory consumed/execution: 800 MB
            - Avg. execution duration: 1.5 seconds
            - Total seconds: 9m * 1.5 sec => 13.5m sec
            - Total GB / sec: 13.5m * 0.8 = 10.8m
            - Free grant per month: 10.8m - 400k free grant = 10.4m GB/sec
            - Payment for execution time: 10.4m * 0.000016$ = 166.4$
            - Payment for executions: 9m - 1m free grant = 8m * 0.2$ / m = 1.6$
            - Total payment: 168$
        - Downsides of the consumption plan:
            - 1.5 GB limit
            - Cold start
            - Limited execution duration (default timeout 5 min, max 10 min)
            - No VNet integration
    - Flex Consumption:
        - Built on the strengths of the serverless Consumption plan, which include dynamic scaling and execution-based billing
        - Extra features:
            - Reduced cold start times when we enable **always-ready** instances
            - Support for VNets: serverless apps can run inside a VNet
            - Per-function scaling: each function in our app scales independently based on its workload, potentially resulting in more efficient resource allocation
            - Improved concurrency handling: better handling of concurrent executions with configurable concurrency settings per function
            - Flexible memory configuration:
                - Offered options:
                    - 512 MB / 0.25 vCores
                    - 2048 MB / 1 vCore
                    - 4096 MB / 2 vCores
            - Mountable file shares
    - Premium:
        - We pay for pre-warmed instances (hosts)
        - Pricing scheme:
            - vCPU duration
            - Memory duration
        - When using premium plan, we can select the instance size for the host
        - We also pay for used scale-out instances
        - With premium plan we get:
            - No cold starts
            - No memory limit (up to the host RAM)
            - Better performance
            - VNET integration
            - Predictable price
        - Downsides of the premium plan:
            - More expensive
    - Dedicated plan:
        - The functions run on an existing App Service
        - To avoid disabling the functions in the App Service settings we have to make sure `Always on` setting is enabled
        - Best when we already have an App Service and want to reuse its unused compute capacity
        - Downsides:
            - There is no autoscale support for the dedicated plan

## Choosing a Hosting Plan (AZ-305)

- **Consumption**: cheapest, true pay-per-use, automatic scaling => spiky/unpredictable workloads where cold start is acceptable
- **Flex Consumption**: serverless billing + VNet integration + always-ready instances => need networking or reduced cold starts without paying for Premium
- **Premium**: pre-warmed instances, no cold start, VNet integration, longer/unbounded execution => consistent or high-performance workloads
- **Dedicated (App Service plan)**: predictable cost on existing App Service, full control, but no event-driven autoscale
- Key decision factors: cold start tolerance, VNet/private networking need, execution duration, memory requirements, and cost predictability

## Scaling and Concurrency

- Consumption and Premium plans scale automatically via the **scale controller**, which adds/removes instances based on event rate
- Each instance can process multiple concurrent executions
- Scale limits: Consumption up to 200 instances, Premium up to 100 instances (per Function App)
- Flex Consumption supports **per-function scaling** (each function scales independently)

## Security and Networking

- Authentication/authorization via **App Service Easy Auth** (Microsoft Entra ID, social providers)
- HTTP-triggered functions are secured with **function keys** (function, host, master) or Entra ID
- Use **Managed Identities** to access other Azure resources without storing credentials
- Secrets should be stored in **Azure Key Vault** (referenced via app settings)
- VNet integration and private endpoints available on **Flex Consumption, Premium and Dedicated** plans (not on classic Consumption)

## Monitoring

- Integrated with **Application Insights** for logging, metrics, distributed tracing and live metrics
- Recommended to enable Application Insights for any production Function App

## Durable Functions

- Stateful functions that interact with external resources and keep track of flow
- Offer very simple syntax, hide complexities of managing state, retries, etc.
- Patterns:
    - Function Chaining: call various functions sequentially and apply the output of each function to the next one
    - Fan-out/Fan-in: run multiple functions in parallel, then aggregate the results
    - Async HTTP APIs: trigger a long-running operation and poll for its status
    - Monitor: a recurring process that watches for a condition and reacts
    - Human Interaction: workflow waits for external input (e.g., approval) with timeouts