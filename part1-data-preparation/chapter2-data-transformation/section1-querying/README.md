# AWS Athena on top of AWS Glue

Glue is for table definitions and ETL. Serverless system that manages table definition(schema) automatically. Using Glue, you can give structure to unstructured data. Then, AWS Athena, the serverless SQL query engine for S3 data can be utilized to analyze it. So, it provides good pipeline for ad-hoc queries before actual ETL job definition.

## AWS Glue Crawler

How the data is organized(or, how it is partitioned; which feature will be primarily used to query data) will determine the efficiency of Glue crawling job.

* Glue cralwer : scans data in S3 and creates schema
* Glue Data Catalog : stores table definition

## Amazon Athena

uses presto under the hood

* no charge for DDL(e.g. data definition like CREATE, ALTER, DROP), and cost applied by mass of scanned data
* no need to copy or load data; it stays in S3 in any data format(ex. parquet).
* to boost performance...
    * just use columnar storage data like ORC or parquet to save money and get better performance
    * also, partition data well(small number of large files is better than large number of small files)
    * `MSCK REPAIR TABLE` : adding partitions after the data is stored
* it sees glue data catalog(including partition), and it automatically creates a table from it so data can be un/semi-structured.

## Athena Services 

* Athena workgroups : organizes users/teams/apps and control query access and track costs.
* CTAS syntax in Athena : CREATE TABLE __ WITH (location, format, compression etc) AS SELECT ...
* ACID transactions : add `table_type = 'ICEBERG'` to make Athena act as if it is strict RDS
    * using this feature, user can periodically compact data by using `OPTIMIZE table REWRITE DATA USING BIN_PACK` query and improve performance of ACID transactions