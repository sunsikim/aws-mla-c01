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

* Resource : bucket or objects specified by ARN
* Effect : Allow / Deny
* Action : API action to allow or deny(ex. s3:GetObject)
* Principal : account or user to apply policy

### Encryption



## 2. Data Backup



### Versioning



### Replication
