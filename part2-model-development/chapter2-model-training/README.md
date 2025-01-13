# Model Training in Sagemaker

Successful model training consists of Preprocess, Training and Tuning jobs. Sagemaker provides management tools for these jobs, whose workflow can be summarized as below.

1. Raw data in S3 + Processing code image in ECR -> S3 URL of converted training data(ex. protobuf)
1. Training data in S3 + Training code image in ECR -> Sagemaker Training(model artifact saved in S3)
1. Model artifact in S3 + persistent endpoint -> make individual predictions
1. Model artifact in S3 + Sagemaker Batch Transform -> batch inference

### Sagemaker Input mode

There are several options to feed data during training job.

* S3 based : can optimize performance using *S3 express one zone*.
    * **S3 File Mode** : (default) copies data into Docker container(has to wait for copy process, and container image gets heavy)
    * **S3 Fast File Mode** : no copy but feed data as stream(works best with sequential data access; not random access)
* Network storage based : requires VPC for data transfer
    * **FSx Lustre** : suitable for training job that requires low latency with 100GB+ throughput in a single AZ
    * **EFS** : when data is stored in EFS
