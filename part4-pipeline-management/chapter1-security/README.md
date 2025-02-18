# Security Management

On top of protecting data in transit using TLS protocol(HTTPS), there are several options to consider when enhancing security around ML pipeline.

## Sagemaker

* everything under /opt/ml and /tmp can be encrypted with a KMS key
* Sagemaker notebooks are internet-enabled by default, which can be a security hole. If disable this option to enhance security, your VPC needs an interface endpoint(PrivateLink) or NAT gateway and allow outbound connections
* Training and Inference containers are also internet-enabled by default. Network isolation is an option, but it also prevents S3 access.
* Can use private VPC when training model with custom endpoint policies

## IAM

* principle of least privilege : constraining allowed access to required actions only(to specify such actions, use *IAM access analyzer*)
* Users or Groups can be assigned JSON documents called policies
* Groups only contain users, not group
* Users don't have to belong to a group, and can belong to multiple groups
* IAM Role : Permission applied on AWS service that performs actions on users' behalf

## Encryption

Types of encryption

* encryption in flight : data is encrypted(TLS) before sending and decrypted after receiving
* server-side encryption : data is encrypted after being received by the server, and decrypted before being sent
* client-side encryption : client takes care of encryption & decryption

Server-side encryption uses key that KMS takes care of(CloudTrail can audit usage of KMS key)

* Symmetric : single key is used for both encryption and decryption(user cannot access to unencrypted KMS key)
* Asymmetric : public(encrypt) and private(decrpyt) key pair(to encrypt data on user side)

There's AWS managed key which handles en/decryption by default, but user can create their own customer key($1 per month, $3 per 1M API calls). Key rotation policy:

* AWS managed KMS key : automatic every 1 year
* Customer managed KMS key : automatic(if enabled) & on-demand
* Imported KMS key : only manual rotation possible

## AWS managed service

* Macie : data in S3 -> Macie(discovers sensitive data like PII) -> notify to Amazon EventBridge
* Secrets Manager : takes care of lifecycle of RDS secrets(ex. RDS user password) -> capability to force rotation of secrets, automate generation of secrets using Lambda
* WAF : web application firewall protects the application from web exploits by defining web ACL(web access control list)
* Shield : prevents DDoS attack(many requests at the same time to overload the server)

## VPC

region level private network to deploy resources

* subnets : AZ level resource to partition the network within VPC
    * public : can accessible via internet <-> internet gateway via route tables
    * private : cannot accessible via internet
        * NAT gateway : connect to internet while remaining private(private <-> NAT gateway in public subnet <-> IGW)
        * VPC endpoints : connect to AWS services instead of www network(VPC endpoint gateway for S3 & DynamoDB and VPC endpoint interface for the rest)
* NACL(Network ACL) : a firewall which controls traffic from and to subnet(attached to subnet)
    * only have ALLOW or DENY rules on IP addresses
* Security Groups : a firewall which controls traffic from and to EC2 instance
    * only have ALLOW rules on IP addresses or other security groups
* VPC flow logs : capture information about IP traffic going into your interfaces for monitoring purpose

VPC peering allows two different VPCs(even from different account) to be connected as if they are in the same network

* must not have overlapping CIDR
* it is not transitive(i.e. although A <-> B and A <-> C, not B <-> C)