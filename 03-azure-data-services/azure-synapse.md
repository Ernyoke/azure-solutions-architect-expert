# Azure Synapse Analytics

- It is a data warehouse and unified analytics platform
- Offers a code free visual environment that streamlines the building of ETL/ELT processes
- Allows the ingestion of data from more than 95 native connectors
- It is deeply integrated with Apache Spark, combining SQL pools (T-SQL) and Spark pools (Spark SQL/Python/Scala/.NET) as distinct engines that can both work over the same lake data
- Supports multiple languages such as T-SQL, Python, Scala, Spark SQL and .Net
- It is integrated with AI and Business Intelligence (BI) tools, such as:
    - Azure Machine Learning
    - Azure Cognitive Services (for AI capabilities such as vision, language and speech)
    - Microsoft Power BI
- Identity and access is managed via Microsoft Entra ID, Azure RBAC and managed identities
- Data is stored in Azure Data Lake Storage Gen2, while we can manage Azure Synapse via Synapse Studio interface

## Synapse SQL

- It is a distributed version of T-SQL designed for data warehouse workloads
- Extends T-SQL to address streaming and machine learning scenarios
- Uses built-in streaming capabilities to land data from cloud data sources into SQL tables
- Integrates AI with SQL by using ML models to score data using T-SQL PREDICT function
- Offers both serverless and dedicated resource models:
    - For unpredictable workloads (unplanned or burst) we can use the always-available serverless SQL endpoints
    - For predictable workloads we can create dedicated SQL pools to reserve processing power for data stored in SQL tables

## Dedicated SQL Pool

- Dedicated SQL pool is a query service over the data in our data warehouse
- The unit of scale is an abstraction of compute power that is known as **data warehouse unit (DWU)**
- Once the dedicated pool is created, we can import big data with simple PolyBase T-SQL queries

## Serverless SQL Pool

- Serverless SQL pool is a query service over the data in the data lake
- Scaling is done automatically to accommodate query resource requirements
- Serverless SQL pool is resilient, it constantly adapts to ensure every query receives the resources it needs

## Apache Spark for Synapse

- Azure Synapse can deeply and seamlessly integrate with Apache Spark
- Apache Spark offers ML models with SparkML algorithms and AzureML integration for Apache Spark 3.1 with built-in support for Linux Foundation Delta Lake
- Offers a simplified resource model that frees us from having to worry about managing clusters
- Fast Spark start-up and aggressive autoscaling
- Synapse removes the traditional technology barriers between using SQL and Spark together => we can seamlessly mix and match based on what we need
- Tables defined on files in the data lake are seamlessly consumed by either Spark or Hive
- SQL and Spark can directly explore and analyze Parquet, CSV, TSV and JSON files stored in the data lake

## Azure Synapse Link for Cosmos DB

- Azure Synapse Link is a feature of Azure Synapse that provides seamless integration and real-time analytics capabilities between Synapse and operational data stored in Cosmos DB
- Allows users to explore and analyze data with no extraction, transformation and loading process required, no data duplication and no impact on the performance of transactional workloads
- It accomplishes this by creating a real-time updated, columnar-based analytics store within Cosmos DB
- Benefits of Azure Synapse Link for Cosmos DB:
    - Real-time Analytics
    - Operational and Analytical Data Cohesion: no need for complex ETL
    - Cost Efficiency
    - Increased Productivity
    - Improved Data Freshness
- When to use Azure Synapse Link for Cosmos DB:
    - We are an Azure Cosmos DB user wanting to conduct analytics, BI and ML on our operational data
    - We are running analytics, BI on Cosmos DB operational data using separate connectors
    - We are executing ETL processes to transfer operational data into a distinct analytics system
- Not ideal if we require traditional data warehouse capabilities like high concurrency, workload management and persisting aggregates across multiple data sources