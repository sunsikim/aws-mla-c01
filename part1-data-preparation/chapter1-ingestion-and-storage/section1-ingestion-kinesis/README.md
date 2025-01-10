# Kinesis topics

There are three services related to this streaming data processing product. In high level, data sent by *producer* is temporarily stored in *Kinesis Data Stream* and then sent to *consumer* like *Data Firehose* or *Managed Service for Apache Flink*.

1. Kinesis Data Stream
    * (1) producer, consumer (2) no deletion, only expiration (3) for small real-time data
    * [Capacity Mode](#capacity-mode) : (1) Provisioned (2) On-demand
    * [Troubleshooting](#troubleshooting) 
1. Amazon Data Firehose
    * (1) no replay (2) fault tolerance (3) supported data types
1. Amazon Managed Service for Apache Flink
    * (1) ETL on Streaming (2) continuous metric generation
 
## Kinesis Data Stream

Use *shards* in *streams* to collect real-time data from *producer* and hold it so that it can be consumed by *consumer*. Data is encrypted using base64 on the fly. Every data has *partition ID*, and data sharing the same ID is guranteed to be ordered.

* **producer, consumer** : data -> *producer* -> stream -> *consumer*
    * Producer includes Kinesis Agent, applications implemented with AWS SDK or *AWS KPL*(Kinesis Producer Library; set of tools to build optimized producer application)
    * Consumer includes *Kinesis Data Firehose*, *Kinesis Data Analytics*, *AWS Lambda function* and application implemented with AWS SDK or *AWS KCL*(Kinesis Consumer Library; set of tools to build optimized consumer application)
* **no deletion, only expiration** : Consumers of the stream can reconsume data in the stream for certain period of time(up to 1 year). To ensure this, data is not deleted within this time window; no deletion, only expiration.
* **for small real-time data** : Maximum size of data send to shard in a stream is 1MB. To process data larger than this, use *AWS Managed Service for Kafka(MSK)*.

### Capacity mode

* **Provisioned mode** : Select number of shards in a stream manually and pay per shard per hour(fyi; each shard gets 1MB/s read and 2MB/s out)
* **On-demand mode** : AWS monitors 30 day amount history and set optimal throughput for automatic scaling(pay per stream per hour and data i/o in GB)

### Troubleshooting

Performance boost can be naively accomplished by increasing the number of shards in a stream, but for more fine-grained troubleshooting, refer to official [producer](https://docs.aws.amazon.com/streams/latest/dev/troubleshooting-producers.html), [consumer](https://docs.aws.amazon.com/streams/latest/dev/troubleshooting-consumers.html) side documentations.

## Amazon Data Firehose

Fully managed service to stack data sent from any producers on *buffer* and *flush* it to preferred destination(ex. S3, OpenSearch, Redshift, HTTP endpoint) as *near real-time* batch process. 

* **no replay** : Does not have data storage capability; if data is processed and flushed to destination, this data is no longer visible from Firehose.
* **fault tolerance** : In case where error occurs during the process, Firehose can send this failed data to S3, as Firehose itself does not have storing capacity.
* **supported data types** : Stacked data can be converted into CSV, JSON, Avro, Parquet. Or, custom data transformation using AWS Lambda function is also possible.

## Amazon Managed Service for Apache Flink

Service to query or process data on stream. Specifically, data flows like source(ex. KDS) -> MSAF -> sink(ex. S3, Firehose). User can develop their own Flink application by themselves and load into MSAF via S3. Use cases include (1) ETL on streaming data (2) continuous metric generation and (3) responsive data analysis.