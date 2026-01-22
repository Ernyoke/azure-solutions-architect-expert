# Azure Functions

- Azure Functions are small, focused functions running as a result of an event => great for event-driven systems
- Azure Functions are automatically managed by Azure => Azure can start, stop and autoscale them
- Azure Functions are have flexible pricing plans
- They are **serverless**

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
        - Server Bus
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
- This lis is growing as other languages are supported in the future

## Cold Starts

- Azure Functions are completely managed by Azure => after some time of inactivity Azure might take down the function's host
- The next activation of the function will take time (2-3 seconds before the code runs)
- How to avoid cold starts?
    - Select the right hosting plan

## Hosting Plans

- Hosting plans are:
    - Consumption:
        - We pay only for what we actually use: execution time (GB/sec) + total number of executions;
        - Consumption plan has a max limit of 1.5 GB of RAM
        - Price calculation example:
            - Execution per month: 9 million
            - Avg. memory consumed/execution: 800 MB
            - Avg. execution duration: 1.5 seconds
            - Total seconds: 9m * 1.5 sec => 13.5m sec
            - Total GB / sec: 13.5m * 0.8 = 10.6m
            - Free grant per month: 10.8m - 400k free grant = 10.4m GB/sec
            - Payment for execution time: 10.4m * 0.000016$ = 166.4$
            - Payment for executions: 9m - 1m free grant = 8m * 0.2$ / m = 1.6$
            - Total payment: 168$
        - Downsides of the consumption plan:
            - 1.5 GB limit
            - Cold start
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
        - Downsides:
            - There is no autoscale support for the dedicated plan

## Durable Functions

- Stateful functions that interact with external resources and keep track of flow
- Offer very simple syntax, hide complexities of managing state, retries, etc.
- Patterns:
    - Function Chaining: call various functions sequentially and apply the output of each function to the next one