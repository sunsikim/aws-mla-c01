# S3 Topics

* *Objects* are stored into a *bucket*
* Although *bucket* is created in certain region, its name has to be unique globally
* Single *object* can be up to 5TB. However, if single *object* is larger than 5GB, *multi part upload* must be used
* Object can include metadata such as tags and version ID.

1. [Security](#1-security)
    * [JSON Policy Format](#json-policy-format)
    * [Encryption](#encryption)
1. [Data Backup](#2-data-backup)
    * [Versioning](#versioning)
    * [Replication](#replication)
1. [Optimizations](#3-optimizations)
    * [Cost Optimization](#cost-optimization)
    * [Performance Optimization](#performance-optimization)
1. [Utilities](#4-utilities)
    * [Event Notifications](#event-notifications)
    * [Access Points](#access-points)

## 1. Security

Regardless of security settings enabled by bucket policy, if *Block public access* is enabled, any publicly opened roles or polices are ignored. This is a additional safety layer that prevents potential data leak which can be caused by mistakes.

### JSON Policy Format

[AWS Policy generator](https://awspolicygen.s3.amazonaws.com/policygen.html) can be used to generate JSON string that contains following items to specify permitted IAM user, role and corresponding actions.

* **Resource** : bucket or objects specified by ARN
* **Effect** : Allow / Deny
* **Action** : API action to allow or deny(ex. s3:GetObject)
* **Principal** : account or user to apply policy

### Encryption



## 2. Data Backup

Protecting data from potential loss is essential for compliance, so this topic shold not be neglected.

### Versioning

Provides easy way to rollback deletion, so prevents unintended data loss.

* **Bucket level** : Enabling versioning object by object (X) Every object in the bucket is versioned (O)
* **Version ID** : When uploading an object with same key, existing object is not deleted by overwriting. Instead, version ID for existing object is created(fyi, initial version ID for any object is null)
* **Delete marker** : If you delete an object, it just leaves delete marker. Deletion can be undone by deleting this delete marker. However, if you delete a version, this is permanent delete.

### Replication

Must enable versioning in both source and target buckets. Target bucket can be a bucket of different account. Replication happens asynchronously(i.e. it takes a few seconds)

* **Bucket region** : If region of source and target buckets are different, it's cross region replication(CRR), otherwise same region replication
    * **CRR** : make copy of a data to prevent potential data loss occur in a region(such fault tolerance is essential for compliance)
    * **SRR** : create a sandbox environment(i.e. dev, staging environment)
* **Optional delete marker replication** : Default replication behavior is to ignore any deletion from source bucket. Delete marker can be replicated, but permanent delete is not replicated.
* **New objects only** : Replication starts from the time of replication rule definition. To replicate existing objects in source bucket, use *S3 Batch replication* service.

## 3. Optimizations

Reliability of S3 storage can be expressed by following measures

* Durability : chance of object stored in S3 preserved(11 9s guranteed; 99.999...99%)
* Availability : measure of readiness of service(differs across different storage classes)

### Cost Optimization

Depending on access frequency, user can select object-by-object storage class among following table.

|                | standard | standard-IA | Glacier Instant Retrieval | Glacier Flexible Retrieval                   | Glacier Deep Archive                       |
|----------------|----------|-------------|---------------------------|----------------------------------------------|--------------------------------------------|
| Storage Cost   | 1        | 0.5         | 0.17                      | 0.17                                         | 0.04                                       |
| Retrieval Cost | 1        | 25          | 250                       | 1                                            | 1                                          |
| Retrieval Time | 0        | 0           | 0                         | Expedited(<=5m), Standard(<=5h), Bulk(<=12h) | Expedited(x), Standard(<=12h), Bulk(<=48h) | 

* Combined with versioning, glacier classes can be optimal choice for noncurrent level version storage.
* **Intelligent tiering** : Used to automatically shift storage class from standard -> IA(30 days) -> glacier(90 days) based on *latest access date*, with additional cost for object monitoring
* **Lifecycle rule** : Manual rule applied on every object in bucket to shift storage class or delete object based on object *creation date*.

### Performance Optimization

* Gurantees at least 3,500 PUT/POST/DELETE/COPY API response per second for each prefix
    * **multi part upload** : divide big file into small sub-parts and upload each in parallel(recommended for 100MB+ file, required for 5GB+ file)
    * **transfer acceleration** : useful when uploading file to bucket in different region(compatible with multi part upload); first upload file into edge location in the same region, then transfer the data to target bucket using private AWS network
* Gurantees at least 5,500 GET/HEAD API response per second for each prefix
    * **Byte range fetches** : parallelize GET request by requesting specific byte range in parallel

## 4. Utilities

### Event Notifications

Used to trigger designated operation when certain S3 API is called(ex. s3:ObjectCreated). 

* Takes few seconds to minutes for notice to be generated.
* Setting proper IAM SNS, SQS, Lambda resource policy on S3 bucket is required.
* Or, user can utilize AWS EventBridge to trigger 18+ AWS service as destination.

### Access Points

