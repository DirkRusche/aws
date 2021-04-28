# AWS

All icons and images are owned by AWS.

## Todos

- [ ] Describe who is the audience of this document
- [ ] Short introduction (what is AWS, comparison to competitors)
- [ ] S3 Storage Classes as Table?
- [ ] Make Overview clickable
- [ ] Sort services
- [ ] Check EC2 section again


## Overview

- Region
- RDS
- SQS
- SNS
- Kinesis
- EC2
- ECS
- ECR
- Fargate
- EKS
- ASG
- SG
- VPC
- Systems Manager
- ElastiCache
- Route 53
- ALB
- S3
- Lambda
- Cloudfront
- DynamoDB
- IAM
- WAF

## [Regions](https://aws.amazon.com/about-aws/global-infrastructure/regions_az)

![AWS Regions worldwide](images/regions_worldwide.png)

![AWS Regions EMEA](images/regions_emea.png)

- Global Infrastructure
- Region = Location
- Region consists of multiple AZs (**A**vailability **Z**ones)
- Near us (Germany): 
  
  Code | Name | Country | Since
  --- | --- | :---: | :---:
  `eu-central-1` | Frankfurt | Germany 🇩🇪 | 2014
  `eu-west-1` | Ireland | Ireland 🇮🇪 | 2007
  `eu-west-2` | London | UK 🇬🇧 | 2016
  `eu-west-3` | Paris | France 🇫🇷 | 2017
  `eu-south-1` | Milan | Italy 🇮🇹 | 2020
  `eu-north-1` | Stockholm | Sweden 🇸🇪 | 2018
  `eu-east-1` | ? | Spain 🇪🇸 | Upcoming (2022/2023)

- AZs are physically separated from each other
  - Connected via low latency links
  - Marked by a letter behind region code
    - eg.: `eu-central-1[a,b,c]`
    - Warning: `eu-central-1a` is not an unique identifier (depends on the account) [read more](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html)
  - Traffic within one AZ is free (rule of thumb)

## [S3 (Simple Storage Service)](https://aws.amazon.com/s3/) ![S3 Icon](aws_icons/s3.png)

- Cloud block storage
- Arbitrary number of files, each up to 5TB per file
- `99.999999999%` (11 9s) durability (= 10 million objects saved over 10,000 years -> expectation: 1 is lost)
- Different storage classes
  - `Standard` - Low latency and high throughput
  - `Intelligent Tiering` - Automatically optimizes storage costs (dependencing on access pattern)
  - `Standard-IA` (IA = Infrequent-Access) - Same as Standard but less availability 
  - `One Zone-IA` - Only one AZ -> less availability
  - `Glacier` - Very cheap; retrieval time at least a few minutes
  - `Glacier Deep Archive` - cheapest option, at least 12 hours retrievel time
  - Dictates the [price](https://aws.amazon.com/s3/pricing/)
    - Standard = `$0.0245/GB`
    - Glacier Deep Archive = `$0.0018/GB` (92,65% less)
- S3 only knows of keys, eg. when you store `path/to/a/file` this (=`path/to/a/file`) is also the key, but the S3 management UI shows `path`, `to` and `a` as a directory
- Versioning, Encryption, Lifecycle Management, Events

## [SQS (Simple Queue Service)](https://aws.amazon.com/sqs/) ![SQS Icon](aws_icons/sqs.png)

- Decouples systems, messages have to be pulled
- Two types
  - `Standard`: 3,000 messages / sec, at least once (occasionally more than one time), best effort ordering
  - `FiFo`: 300 messages / sec, exactly once, **f**irst **i**n **f**irst **o**ut
- Visibility timeout while processing (after message pull)
- Batching of messages possible, max payload `256KB`
- Max. message retention 14 days
- DLQ (**d**ead **l**etter **q**ueue): unprocessable messages are automatically moved

## [SNS (Simple Notification Service)](https://aws.amazon.com/sns/) ![SNS Icon](aws_icons/sns.png)

- Pub / Sub (Topics)
- Standard, FiFo
- Filtering, Fanout
- Push to
  - Lambda
  - SQS
  - Kinesis
  - HTTP
  - Email
  - SMS
- In comparison to SQS this is push (whereas SQS is pull)

## [Kinesis (Data Streams)](https://aws.amazon.com/kinesis/data-streams) ![Kinesis Icon](aws_icons/kinesis.png)

- Kinesis Video Streams, **Kinesis Data Streams**, Kinesis Data Firehose, Kinesis Data Analytics
- Real time (within 70ms)
- Multiple producer, multiple consumer
- Shard is base throughput unit (`1MB/s` write, `2MB/s` read)
- Every event has a partition key (is used for routing onto a shard; usually user ID)

## [RDS (Relational Database Service)](https://aws.amazon.com/rds/) ![RDS Icon](aws_icons/rds.png)

- Different engines available
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL Server
  - PostgreSQL
  - Aurora
    - MySQL or PostgreSQL **compatible** (it's a fork)
    - Serverless (‘can sleep’), automatic scaling
- Read replicas (performance)
- Multi-AZ deployments (availability, durability)
- Automated backups, snapshots
- Maintenance window (applying backups and stuff, downtime possible)

## [DynamoDB](https://aws.amazon.com/dynamodb/) ![DynamoDB Icon](aws_icons/dynamodb.png)

**TODO**

## [EC2 (Elastic Compute Cloud)](https://aws.amazon.com/ec2/) ![EC2 Icon](aws_icons/ec2.png)

- Many AMI (**A**mazon **M**achine **I**mage) available
- Different Instance types
  - General Purposes
  - Compute Optimized
  - Memory Optimized
  - Accelerated Computing (GPU)
  - Storage Optimized
  - [Nice EC2 instance type overview](https://instances.vantage.sh/) (former ec2instances.info)
  - Max is `U-24TB1 Metal`: `448` vCPU, `24576GB` RAM
- Storage options
  - Instance Storage - fastest
  - EBS (Elastic Block Storage) - can only be attached to one instance
  - EFS (Elastic File System) - can be shared by multiple instances
- Hibernation
- Spot Instances, Saving Plans, Reserved Instances

## [ASG (Auto Scaling Group)](https://aws.amazon.com/autoscaling/) ![EC2 ASG Icon](aws_icons/ec2_asg.png)

- Minimum, desired and maximum capacity
- Will ensure the desired capacity at all time
- Three different scaling strategies: manual, dynamic, scheduled
- Manual scheduling
  - Keep desired capacity all the time
- Dynamic scheduling
  - Target tracking scaling: maintain a certain value of a metric (and increase or decrease desired capacity based on that metric)
  - Step scaling: increase or decrease desired capacity based on a set of scaling adjustments
  - Simple scaling: increase or decreased desired capacity on a single scaling adjustment

## [ECS (Elastic Container Service)](https://aws.amazon.com/ecs/) ![ECS Icon](aws_icons/ecs.png)

- Can run on EC2 or Fargate (serverless)
- ECS consists of clusters
  - Cluster contains Services
  - Service contains Tasks (=Instances) (Kubernetes: Pod)
  - Task contains Containers
- Deployment ensures availability (you can set a minimum of healthy tasks in percent)
- Failed tasks are automatically replaced

### [ECR (Elastic Container Registry)](https://aws.amazon.com/ecr/) ![ECS Icon](aws_icons/ecr.png)

Can be used to store and provide Docker Images

### [EKS (Elastic Kubernetes Service)](https://aws.amazon.com/eks/) ![ECS Icon](aws_icons/eks.png)

Managed Kubernetes Service


## [VPC](https://aws.amazon.com/vpc/) ![VPC Icon](aws_icons/vpc.png)

**TODO**

### SG (Security Group)

**TODO**

### NACL

**TODO**

## [Systems Manager (Parameter Store)](https://aws.amazon.com/systems-manager/) ![Systems Manager Icon](aws_icons/systems-manager.png)

- Key Value store
- Typically used for storing passwords, configuration etc.
- Datatypes: `String`, `StringList`, `SecureString`
- `SecureString` uses KMS (Key Management Service) to encrypt value
- Can be automatically retrieved by EC2, ECS, Lambda, ...

## [ElastiCache](https://aws.amazon.com/elasticache/) ![ElastiCache Icon](aws_icons/elasticache.png)

- Redis or Memcached
- Maintenance window for applying patches
- Parameter group to define engine configuration values
- Cluster, Read Replica, Multi-AZ

## [Route 53](https://aws.amazon.com/route53/) ![Route53 Icon](aws_icons/route53.png)

**TODO**

## [ELB](https://aws.amazon.com/elasticloadbalancing/) ![ELB Icon](aws_icons/elb.png)

**TODO**

## [Lambda](https://aws.amazon.com/lambda/) ![Lambda Icon](aws_icons/lambda.png)

**TODO**

## [Cloudfront](https://aws.amazon.com/cloudfront/) ![Cloudfront Icon](aws_icons/cloudfront.png)

**TODO**

## [IAM](https://aws.amazon.com/iam/) ![IAM Icon](aws_icons/iam.png)

**TODO**

## [WAF](https://aws.amazon.com/waf/) ![WAF Icon](aws_icons/waf.png)

**TODO**
