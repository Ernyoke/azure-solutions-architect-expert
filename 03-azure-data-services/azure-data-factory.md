# Azure Data Factory

- It is a managed service for ETL/ELT and data itegration
- Can create data-driven workflows for orchestrating data movement and transforming data at scale
- With Data Factory we can:
    - Create Pipelines to schedule data-driven workflows
    - Build complex ETL processes that transform data visually with data flows
    - Use compute services such az Azure HDInsight Hadoop, Azure Databrics and Azure SQL Database
    - Publish transformed data to data stores such as Azure Synapse Analytics

## Core Components

- Pipelines:
    - A logical gorup of activities that perform a unit of work
- Activities:
    - Processing steps in a pipeline
- Datasets:
    - Data structures within the data store
- Linked services:
    - They define the connection information for data sources to connect to Data Factory
- Data flows:
    - Logic to define how data moves through pipeline/how the data is transformed
    - Offers a visual journey of the data flow and manipulations
- Integration Runtime (RI):
    - Compute infrastructure used by Azure Data Factory
- Control flow:
    - Orchestrates the sequence of activities in a pipeline

## Data Orchestration Process

1. Data Ingestion: 
    - Data is ingested from various sources, like on-premises SQL Server databases or external data
    - Data is pulled using connectors in either batch or real-time modes
2. Data Storage and Transformation:
    - Post ingestion, data is stored for processing in Azure Blob Storage or Azure Synaps Analytics for more structured data
    - ADF pipelines transform this data by cleaning and enriching it
3. Analysis:
    - The processed data is loaded into Azure Analysis Services for analysis, providing unified view of the business data
4. Visualization:
    - The analyzed data is leveraged by tools like Power BI to generate insightful visualizations and reports
5. Security and Authentication:
    - Azure AD is used throughout this process for user authentication and authorization, securing our resources and data

## Microsoft SQL Server Integration Services (SSIS)

- It is a platform for building enterprise-level data integration and data transformation solutoins
- It can be used to:
    - Automate SQL server databases
    - As an integration runtime in Azure Data Factory
- We can perform the following tasks with SSIS:
    - Copy files or donwload files
    - Load data into data warehouses
    - Cleanse data for better accuracy
    - Drive into mining data
    - Manage SQL Server objects and data
- SSIS can perform ELT with variety of sources:
    - XML
    - Flat files
    - Relational data sources
- SSIS has:
    - Built-in tasks and transformations
    - Graphical tools for building packages
    - Integration Services Catalog database: when we store, run and manage packages