# Modern Data Engineering with Medallion Architecture using DBT, Databricks, Spark and Azure Cloud 
In this project, we setup and end to end data engineering using Apache Spark, Azure Databricks, Data Build Tool (DBT) using Azure as our cloud provider. This project illustrate the process of data ingestion to the lakehouse, data integration with ADF and data transformation with Databricks, and DBT.


## System Architecture
![Medallion_architecture_data_lake_house](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/42398cae-60c4-4576-b031-1c980f5b331f)

## Steps involved are:

![home page](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/1e7ac98c-273b-4262-acf1-9c2e2590ee52)

### Creating resource groups on Azure
![resource group](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/6745a08c-5411-4da6-99ae-dc38f7c39e3c)

### Setting up the medallion architecture storage account
- All layers of bronze, silver, and gold layers are added here in the storage group as in containers.
  
![container](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/9b6cca04-d5ca-4f59-9866-7f9ab6b552d7)

### Setting up Azure Data Factory
![linked services](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/163ecefc-6321-4fab-a7ae-fdb605a4fb83)

### Azure Key Vault setup for secrets
- Added all the data into the key, instead of copying it every time.
  
### Azure database with automatic data population
- Created an SQL database(I used an existing sample that Adventure worksLT) and logged in with username and password to fetch the tables.
- I will see the tables in the query editor of the SQL database of sales, product, and customer data.
- I will automate the process to get this data into the Bronze -> Silver -> Gold layers.
  
### Azure Data Factory pipeline orchestration
- The Azure data factory goes into this database server and fetches the table's names, so each of these tables is going to loop through and then dump all data into the bronze layer.
  
![pipeline](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/adc98bcf-bdd1-4398-afda-705076c08ea8)

- created three datasets to fetch the data from tables.
  
![t1](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/2cc14011-76d9-4483-94d7-57bac971a5fa)
![t2](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/1790c04b-32a8-4a65-9ed3-458dfa4debf9)
![t3](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/419b257f-b62c-4c2e-bdf2-1c810f16901c)

- After validating and debugging the data factory now tables have been put into the first layer of bronze in the container.
  
![bronze](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/63c862e9-48d7-4b19-abd9-70b23f77672d)

### Setting up Databricks
- Created new Azure data bricks and launched it, also created a new workspace in that data bricks.

### Azure Databricks Secret Scope and Key Vault
- Created secrets and scope into the new secret page to get the DNS name and Resource ID from the key vault.
- Created a compute with a single node and mounted it into the bronze layer.
- In the Databricks workspace check for storage account key and scope to fetch details.
  
### Verifying Databricks - Key Vault - Secret Scope Integration
- Verified my connection between storage account and data bricks using the below code from my base notebook.
  
dbutils.fs.mount(
     source='wasbs://bronze@medallionsg.blob.core.windows.net',
    mount_point='/mnt/bronze',
     extra_configs={'fs.azure.account.key.medallionsg.blob.core.windows.net':dbutils.secrets.get('databricksScope','StorageAccountKey')}
)
dbutils.fs.mount(
    source='wasbs://silver@medallionsg.blob.core.windows.net',
    mount_point='/mnt/silver',
    extra_configs={'fs.azure.account.key.medallionsg.blob.core.windows.net':dbutils.secrets.get('databricksScope','StorageAccountKey')}
)
dbutils.fs.mount(
    source='wasbs://gold@medallionsg.blob.core.windows.net',
    mount_point='/mnt/gold',
    extra_configs={'fs.azure.account.key.medallionsg.blob.core.windows.net':dbutils.secrets.get('databricksScope', 'StorageAccountKey')}
)

### Azure Data Factory - Databricks Integration
- To create a connection between data bricks and data factory.
- Added Notebook in the pipeline and connected to data bricks base notebook in that added table schema, table name, and filename.
![basenotebook_workspace](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/b60331ec-87f4-48dc-b682-5749d0b9cd7c)

- After validating and debugging the data bricks the data is added from the bronze to the silver layer.
![silver](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/f6958c7a-f99c-4ba0-9ceb-b4a6b41fa5cb)

### DBT Setup
- I used pycharm for dbt setup, installed data bricks, and configured data bricks using the token and host link.
- I have uploaded my Python file above.
  
### DBT Snapshots with Azure Databricks and ADLS Gen2
- from the Azure data bricks catalog table added all the table's names as a snapshot in pycharm.
  
### DBT Data Marts with Azure Databricks and ADLS Gen2
- In marts created 3 folders of customer, product, and sales.
- After testing and running the snapshots and marts in Python file.
- The data is added into the third layer which is gold.
![gold](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/55f3f109-edc8-4932-b581-f947de5de16a)

### DBT Documentation 
- To document this in DBT we have to generate and serve the DBT and it will convert to JSON and open in the local browser.
![project](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/2cda4220-f4a7-4e92-8018-efb89743445c)
![p2](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/34dfe78d-7815-4eb6-a435-007ea0d78cc2)
![p3](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/73d01cce-2ee8-42f4-924f-686be2801ea2)
![p4](https://github.com/vidd01/Azure_pipeline_project/assets/122332733/b09f8680-320c-44f9-a186-af8d84e1f31c)




