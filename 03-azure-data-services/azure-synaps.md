# Azure Synaps Analytics

- It is a data warehouse and unified analytics platform
- Offers a codefree visual environenment that streamlines the building of ETL/ELT processes
- Allows the ingestion of data from more than 95 native connectors
- It is deeply integrated with Apache Spark, allowing the use of T-SQL queries on both our data warehouse and Spark engines
- Supports multiple languages such as T-SQL, Python, Scala, Spark SQL and .Net
- It is integrated with AI and Business Intelligence (BI) tools, such as:
    - Azure Machine Learning
    - Azure Cognitive Services (for identity and access management)
    - Microsoft Power BI
- Data is stored in Azure Data Lake Storage Gen2, while we can manage Azure Synaps via Synaps Studio interface

## Synaps SQL

- It is a distributed version of T-SQL designed for data warehouse workloads
- Extends T-SQL to address streaming and machine learning scenarios
- Uses built-in streaming capabilities to land data from cloud data sources into SQL tables
- Integrates AI with SQL by using ML models to score data using T-SQL PREDICT function
- Offers both serverless and dedicated resource models:
    - For unpredictable workloads (unplanned or burst) we can use the always-avialable serverless SQL endpoints
    - For predicatable workloads we can create dedicated SQL pools to reserve processing power for data stored in SQL tables

## Dedicated SQL Pool

- Dedicated SQL pool is a query service over the data in our data warehouse
- The unit of scale is an abstraction of compute power that is know as **data warehouse unit (DWU)**
- Once the dedicated pool is created, we can import big data with simple PolyBase T-SQL queries

## Serverless SQL Pool

- Serverless SQL pool is query servoce over the data in the data lake
- Scaling is done automatically to accomodate query resource requirements
- Serverless SQL pool is resilient, it constantly adapts to ensure every query receives the resources it needs

## Apache Spark for Synapse

- Azure Synaps can deeply and seamleesy integrate with Apache Spark
- Apache Spark offers ML models with SparkML algorithms and AzureML integration for Apache Spark 3.1 with built-in support for Linux Foundation Delta Lake
- Offers a simpified resource model that frees us from having to worry about managing clusters
- Fast Spark start-up and agressive autoscaling
- Synapse removes the traditional technology barriers between using SQL and Spark togedher => we can seamlessly mix and match based on what we need
- Tables defined on files in the data lake are seamlesslu consumed by either Spark or Hive
- SQL and Spark can directly explore and analyze Parquet, CSV, TSV and JSON files stored in the data lake

## Azure Synapse Link for Cosmos DB

- Azure Synapse Link is a feature of Azure Synapse that provides seamless integration and real-time analytics capabilities between Synapse and operation data stored in Cosmos DB
- Allows users to explore and analyze data with no extraction, transformation and loading process required, no data duplication and no impact on the performance of transactional workloads
- It accomplishes this by creating a real-time updated, columnar-based analytics store within Cosmos DB
- Benefits of Azure Synapcs Link for Cosmos DB:
    - Real-time Analytics
    - Operational and Analytical Data Cohesion: no need for complex ETL
    - Cost Efficiency
    - Increased Productivity
    - Improved Data Freshness
- When to use Azure Synapse Link for Cosmos DB:
    - We are an Azure Cosmos DB user wanting to coduct analytics, BI and ML on our operation data
    - We are running analytics, BI on Cosmos DB operational data using separate connectors
    - We are executing ETL processes to transfer operation data into distinc analytics system
- Not ideal if we require traditional data warehouse capabilities like high concurrency, workload management and persisting aggregates across multiple data sources