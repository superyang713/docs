# A Learning Journal of Amazon Web Services (AWS) -- Part 1

AWS is learding cloud computing platform among other public clouds. AWS is
among the top 5 hot skills in the cloud-computing space. I am right now
self-learning AWS, and in this blog, I will summerize some important services
that AWS offers. My ultimate goal is to become an AWS certified Solutions
Architect.

## Identity Access Management (IAM)

* IAM consists of the following:
    1. Users
    2. Groups (a way to group users and apply policies to them collectively)
    3. Roles
    4. policy Documents.(can be applied to users, groups, or roles individually,
    and made by Json)

* IAM is universal. It does not apply to regions at this time.

* The root account is simply the account created when first setup the AWS
  account. It has complete Admin access.

* New users have no permissions when first created.

* New Users are assigned Accessed Key ID & Secret Access Keys when first
  created.

* The Access Key ID & Secret Access Keys are not the same as user name and
  password. The former cannot be used to login into the console, however, they
  are used to access AWS via the APIs and Command Line.

* The Access Key ID & Secret Access Keys can only be viewed once when they were
  first created. If lost, they have to be regenerated.

* Always setup Multifactor Authentication (MFA) on the root account.

* It is recommended to create and customize your own password rotation policies
  for the users.

## AWS Object Storage and Content Delivery Network -- S3, Glacier and CloudFront

### Overview

* S3 is Object-based: i.e. allows you to upload files, but not applications or
  databases, which are Blocked-based storage.

* Files can be from 0 Bytes to 5 TB.

* There is unlimited Storage.

* Files are stored in Buckets.

* S3 is a universal namespace, i.e. names must be unique globally.

* An example of a bucket url: https://s3-eu-west-1.amazonaws.com/bucket_name

* Read after Write consistency for PUTS of new Objects.

* Eventual Consistency for overwirte PUTS and DELETES (can take some time to
  propagate).

* S3 Storage Tiers:
    1. S3 (durable, immediately available, frequently accessed)
    2. S3 - IA (durable, immediately available, infrequently accessed)
    3. S3 One Zone - IA (even cheaper than IA, but only in once availability zone)
    4. Glacier - Archived data, where you can wait 3 - 5 hours before accessing.

### Create an S3 bucket

* Upload an object to S3 receive an HTTP 200 code.

* S3, S3 - IA, S3 Reduced Redundancy Storage.

* Encryption

    1. Client Side Encryption (1 type)
    2. Server Side Encryption (3 types)

* Control access to buckets using either a bucket ACL or using Bucket Policies.

* By default, buckets are private and all objects stored inside them are private.

### Versioning

* Stores all versions of an object (including all writes and even if you delete an object)

* Great backup tool.

* Once enabled, versioning cannot be disabled, only suspended.

* Integrates with lifecycle rules.

* Versioning's MFA Delete capability, which uses multi-factor authentication,
  can be used to provide an additional layer of security.

### Cross Region Replication

* Versioning must be enabled on both the source and destination buckets.

* Regions must be unique.

* Files in an existing bucket are not replicated automatically. All subsequenty
  updated files will be replicated automatically.

* You cannot replicate to multiple buckets or use daisy chaining at the moment.

* Delete markers are replicated, however, deleting individual versions or
  delete markers will not be replicated.

* Understand what Cross Region Replication is at a high level.

### Lifecycle Management

* Can be used in conjunction with versioning.

* Can be applied to current versions and previous versions.

* A custom lifecycle management policy:

    1. Transition to the Standard - Infrequent Access Storage Class (30 days
       after the creation date.)
    2. Archive to the Glacier Storage Class (30 dyas after IA, if relevant)
    3. Permanently Delete.

### CloudFront

* Edge Location - the location where content will be cached. It is separate to
  an AWS Region/AZ.

* Origi - the origin of all the files that the CDN will distribute. It can be
  either an S3 bucket, an EC2 instance, an Elastic Load Balancer or Route 53.

* Distribution - the name give the CDN which consists of a collection of Edge
  Locations.

    1. Web Distribution - used for websites.
    2. RTMP - used for media streaming.

* Edge locations are not just READ only, you can write to them too (ie put an
  object on to them).

* Objects are cached for the life of the TTL (Time To Live)

* You can clear cached objects, but you will be charged.
