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
