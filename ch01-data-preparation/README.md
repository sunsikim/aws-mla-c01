# Data Preparation

Basic concepts to define properties of data and therefore determine right format of data to hold information and storage system to use

1. [Properties of Data](#1-properties-of-data)
1. [Common Data Formats](#2-common-data-formats)
    1. [text-based formats](#1-text-based-formats)
    1. [binary formats](#2-binary-formats)
1. [Storage Systems](#3-storage-systems)
    1. [Required Jobs: Extract, Transform, Load](#extract-transform-load)
    1. [Comparing Data Warehouse and Data Lake](#data-warehousedw-data-lakedl)

## 1. Properties of Data

Three keywords to describe basic characteristic of certain dataset

* **Volume**: size of data
* **Velocity**: speed of new data being generated, collected and processed
    * ex. need real-time processing <-> batch processing is enough
* **Variety**: type of data
    * *structured*: usually refers to tabular data with predefined schema(ex. CSV, RDBMS table)
    * *semi-structured*: not as organized as structured, so requires some parsing to pull out information from it(ex. XML, JSON)
    * *unstructured*: unorganized data without any predefined schema or parsable structure(ex. image, text)

## 2. Common Data Formats

Classified into two categories

### (1) text-based formats

Human readable, editable and system independent(because any system that can read, write text data can access to the information)

* **CSV** : delimeter separated format
    * shares same abstraction design with RDBMS table(i.e. *row-column structure*)
    * therefore, source and target is usually *small to medium sized RDBMS table*
* **JSON** : key-value format
    * unlike CSV, also suitable for *semi-structured data* because its key-value structure allows nested data structure
    * suitable for data whose *schema is flexible*(i.e. each row can have different keys), so fits well to NoSQL DB like MongoDB

### (2) binary formats

Not human readable, nor editable

* **Avro**: binary data with its schema embedded
    * suitable for system where *schema changes frequently*(information in the data will be preserved even when the schema changes)
    * its binary nature makes serialization more efficient, so *good choice for data transportation*(frequently used in real-time big data processing system like Kafka, Spark, Flink etc)
* **Parquet**: column-based binary data with its schema embedded
    * good choice to *store data in distributed system*(column A in this node while B in that node) where efficient compression comes in column by column
    * *optimizes large scale data analytics*, since only subset of columns is required in most cases

## 3. Storage Systems

On jobs that those systems should do and when to choose what

### Extract, Transform, Load

These processes must be automated in reliable way, and how to implement it using AWS services will be covered in following sections.

* Extract: retrieve raw data from *external source systems* including
    * JDBC(or, ODBC): common interface for connecting external DB 
    * raw log files: stored in S3, parsed according to specific schema
    * external APIs: response, request in JSON format is extracted and stored
    * data stream: when real-time consumption takes place
* Transform: Convert data into a suitable format
* Load: Move (transformed) data into a target storage

### Data Warehouse(DW), Data Lake(DL)

Note that these are gradually considered as legacy approach, since hybrid of two(Data Lakehouse) is becoming a norm for ML data storage system(ex. Redshift Spectrum on S3 objects).

* DW: centralized storage for OLAP
* DL: raw-data native format storage(loaded as-is)

||Data Warehouse|Data Lake|
|-|-|-|
|schema|schema-on-write(predefined schema exists at the time of loading)|schema-on-read(data consumer defines schema on raw data)|
|data type|structured|anything|
|agility|less agile due to predefined schema|adaptable to schema change afterwards|
|processing|ETL|EL(T)|
|use-case|complex queries on large data(optimized for such queries, so suitable for BI tasks)|cost-effective, scalable storage for data whose usage is not yet determined|
|etc|data integration from different sources is essential(otherwise, it's just database)|costs can rise when used to process large amounts of data|



