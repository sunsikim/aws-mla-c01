# Data Wrangling

## AWS Glue Studio

Other than being cataloging tools, Glue it also provides ETL functionality. This uses Spark under the hood, but as this is serverless service, it doesn't require Spark cluster to be managed by user.

* Glue Studio : visual interface based on DAG UI for ETL workflows(go-to choice for scheduling and managing the execution of data processing jobs)
* Glue Data Quality : latest feature of Glue studio that lets users define custom rule using DQDL to detect any low quality data flows in(this result is used to fail the job or report to CloudWatch)
* Glue DataBrew : UI for preprocessing large datasets from S3 or DW(250 ready-made transformation exists). user creates recipes of transformation that can be saved as jobs to be reused.

## SageMaker Data Wrangler

It is a ETL pipeline baked in Sagemaker Studio. Like Glue DataBrew, there are already 300+ built-in transformations for data wrangling. Or, user can define own custom functions with pandas, pyspark etc. Note that it is just code of transformation it provides: it does not actually do the data transformation. These codes intended to be implemented into further Sagemaker processes like Processing, Pipelines etc. 

## EMR

