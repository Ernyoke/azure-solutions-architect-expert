# App Services

- It is a fully managed web hosting for websites and APIs
- With App Service we don't have to create a virtual machine, be responsible for its maintenance and updates, we just simply have to publish our code
- We do not have access to the underlying servers
- App Service underlying infrastructure is "always" secure and compliant. Microsoft is responsible for the security and compliance of the service
- App Service integrates well with many source controls and DevOps engines such as Github, BitBucket, Azure DevOps, DockerHub and many more
- App Services supports the following platforms:
    - .NET/.NET Core
    - NodeJS
    - Java
    - Python
    - PHP
- App Services also supports containers
- App types supported by App Service:
    - Web apps
    - Web API
    - Web Jobs (batch processes)
- App Services are extremely easy to deploy:
    1. We have to develop our app
    2. We have to create a Web App in Azure (can be done from our IDE as well)
    3. We have to publish our code

## App Services Tiers

- When creating an App Service we are asked to select the tier
- Tier options are the following:
    - Free
        - Disk space: 1 GB
        - Compute type: shared
        - Price: free
    - Basic - dedicated for dev/test environments
        - Disk space: 10 GB
        - Maximum instances: up to 3
        - Compute type: dedicated
        - Price: from ~13$/month
    - Standard - for production usage
        - Disk space: 50 GB
        - Maximum instances: up to 10
        - Compute type: dedicated
        - Price: from ~69$/month
    - Premium - enhanced performance and scalability
        - Disk space: 250 GB
        - Maximum instances: up to 30
        - Compute type: dedicated
        - Price: from ~84$/month
    - Isolated - high performance, security and isolation
        - Disk space: 1 TB
        - Maximum instances: up to 100
        - Compute type: isolated (placed in its own virtual network)
        - Price: from ~208$/month

## Auto scaling App Service

- App Service can be autoscaled to support spikes in load
- The auto scaling is based on various metrics
- Auto scaling setup requires similar rules to what we have seen for the VM scale sets

## Pricing Plan

- When we create the App Service, we create 2 components:
    - App Service that hosts our code
    - Plan: infrastructure to host the App Service
- We can reuse the same pricing plan to host multiple App Services

## Vertical scaling

- Azure App Services allows vertical scaling for already deployed applications
- We can change the Pricing Plan to support a more expensive/cheaper tier even after the App Service was created and our app was deployed

## Deployment Slots

- When uploading code to App Service the newest version of the app is accessible immediately
- Sometimes we want to test the new version before publishing it to our consumers
- Deployment Slots allow us to deploy our app and test is separately from the main site
- After validation we can swap the slots and promote the latest version of the app to production
- Number of allowed slots depends on the pricing plan/tier (ie. Standard plan allows up to 5 slots)
- A slot is a fully functional app service with a dedicated URL
- Slots are free, they do not generate additional costs
- Traffic can be split between slots (Blue-Green deployment) => some users will be routed to the production app while other users will be routed to the slot
