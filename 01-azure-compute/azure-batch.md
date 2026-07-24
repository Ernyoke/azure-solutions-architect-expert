# Azure Batch

- Managed service for running **large-scale parallel and HPC (high performance computing) jobs**
- Azure creates and manages a pool of VMs, schedules the work and cleans up afterwards
- The Batch service itself is free - you pay only for the underlying VMs, storage and networking

## Core concepts

- **Batch account** - the top-level resource, holds pools, jobs and quotas
- **Pool** - the collection of compute nodes (VMs) that run the work
- **Job** - a logical grouping of tasks, associated to one pool
- **Task** - a single unit of work (a command line executed on a node)
- **Job schedule** - runs jobs on a recurring schedule
- **Task dependencies** - a task can wait for other tasks to finish (fan-out / fan-in patterns)

## Pools

- Node types: **dedicated** VMs and **low-priority / Spot** VMs (much cheaper, can be pre-empted)
- Fixed size or **autoscale** driven by a formula (for example based on pending task count)
- Virtual Machine Configuration with marketplace or custom images (Shared Image Gallery)
- Containers can be run on Batch nodes (image pulled from ACR or Docker Hub)
- **Start task** runs on every node when it joins the pool (install dependencies)
- **Application packages** distribute binaries to all nodes
- Pools can be deployed into a VNet for private access to data sources

## Data flow

- Input files usually staged from Azure Storage (resource files)
- Output files uploaded back to Storage when a task completes
- Use Azure Files or blob mounts for shared data

## Typical use cases

- 3D rendering and media transcoding
- Monte Carlo / financial risk simulations
- Engineering simulations (CFD, FEA), genomics and research workloads
- Any embarrassingly parallel workload with many independent tasks

## Alternatives

| Option | When |
| --- | --- |
| Azure Batch | Large-scale parallel/HPC, needs scheduling, dependencies and Spot capacity |
| Container Apps Jobs | Containerized, event-driven or scheduled jobs, serverless |
| ACI | A handful of one-off container tasks |
| AKS Jobs/CronJobs | Already running Kubernetes |
| Azure Functions | Short, lightweight, event-triggered work |
| Azure CycleCloud | Traditional HPC schedulers (Slurm, PBS, Grid Engine) lifted to Azure |
