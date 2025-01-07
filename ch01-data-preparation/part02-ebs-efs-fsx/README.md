# EBS, EFS, FSx topics

Unlike S3 which is basically gigantic AWS-managed key-value container of objects, EBS, EFS and FSx can be thought of as a independent file system that user can manage with relatively higher cost.

## EBS(Elastic Block Storage)

Think of it as hard drive, because this analogy mostly fits the property of this storage. *Fast detach and attach*. Note that using the attached volume within EC2 instance is another topic covered in [this page](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-using-volumes.html).

* **single instance in single AZ** : Disk is attached only in one computer, and does not move easily. To move data from EBS to another region, take snapshot of it. However, as of computer, attaching multiple EBS on single instance is available.
* **persists after termination** : Disk does not disappear after computer is turned off. However, such persistence can be optionally turned off using *delete on termination* option. This option is applied by default on root EBS but not on additionally attached EBS.
* **provisioned capacity** : Disk on computer have its own fixed capacity in terms of volume and velocity. However, its type can be modified on the fly(i.e. when instance is running), but not in volume-decreasing way. These are types of EBS provisioned by AWS.

| name | disk type            | performance             | use-case                                     |
|------|----------------------|-------------------------|----------------------------------------------|
| io2  | provisioned iops SSD | throughput 16, i/o 16   | mission critical database service            |
| io1  | provisioned iops SSD | throughput 4, i/o 4     | i/o intensive NoSQL database                 |
| gp3  | general purpose SSD  | throughput 4, i/o 1     | middle-sized RDBMS in production environment |
| gp2  | general purpose SSD  | throughput 1, i/o 1     | staging environment transaction workloads    |
| st1  | HDD                  | throughput 2, i/o 0.03  | large log data ETL server                    |
| sc1  | HDD                  | throughput 1, i/o 0.015 | cold data storage                            |

## EFS(Elastic File System)

Imagine a hard drive that is lives in the word wide web and its files transferred via network. This system would used Linux's mount functionality using NFS protocol, so *only be used in Linux instance*. Also, *storage size would scale automatically*, as data will be stored in wide wide web. Other properties would include:

* **multiple instances in multiple AZs** : After configuring the mount target at specific AZ with appropriate *security group setting(mandatory)*, EFS can be directly connected and mounted to 100+ instances in various AZs. Nevertheless, *one-zone option is also available*.
* **throughput mode** : Since every data is transferred through network, there are three performance options that user can choose based on expected usage.
    * **Bursting** : if data in the system enlarges, there are more chance for larger throughput will be needed. This mode scales throughput based on the size of data the system holds.
    * **Enhanced** : In this setting, user can either fix the throughput that user needs(*Provisioned*) or let the system determine the range of throughput the system would need(*Elastic*).
* **storage classes** : Files in the system can have 3 different storage classes; *standard, EFS-IA* and *Archive*. User can set lifecycle policy based on latest access date, so files not accessed for a long time can be moved to cheaper storage classes for efficient budget use.

## FSx(File Server)

x in FSx seem to mean unknown variable literally, since this is service for third party file system in AWS. List of supported services and their comparisons is well explained in [this page](https://aws.amazon.com/fsx/when-to-choose-fsx/).