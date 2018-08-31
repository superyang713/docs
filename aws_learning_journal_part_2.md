# A Learning Journal of Amazon Web Services (AWS) -- Part 2

## Elastic Compute Cloud (EC2)

* On Demand - allows you to pay a fixed rate by the hour (or by the second)
  with no commitment.

* Reserved - provides you with a capacity reservation, and offer a significant
  discound on the hourly charge for an instance. 1 Year or 3 Year Terms.

* Spot - enables you to bid whatever prive you want for instance capacity,
  providing for even greater savings if your applications have flexible start
  and end times.

* Dedicated Hosts - Physical EC2 server dedicated foryour use. Dedicated Hosts
  can help you reduce costs by allowing you to use your existing server-bound
  software lisences.

* If a spot instance is terminated by EC2, you will not be charged for a
  partial hour of usage. However, if you terminate the instance yourself, you
  will be charged for the complete hour in which the instance ran.

* FIGHT DR MC PX! --> Abbr letters for EC@ instance types.

    1. F --> Field Programmable Gate Array
    2. I --> IOPS
    3. G --> Graphics
    4. H --> High Disk Throughput
    5. T --> Cheap General Purpose
    6. D --> Density
    7. R --> Ram
    8, M --> Main Choice for General Purpose Apps
    9. C --> Compute
    10. P --> Graphics (think Pics)
    11. X --> Extreme Memory

    [ec2 instance](https://www.dropbox.com/s/9hogiqiitoxkfpr/ec2_instance_types.png?raw=1)

* Elastic Block Storage (EBS) types:

    * SSD

        1. General Purpose SSD - balances price and performance for a wide
           variety of workloads.
        2. Provisioned IOPS SSD - Highest performance SSD volume for
           mission-critical low-latency or high-throughput workloads.

### Security Group

* All inbound traffic is blocked by default, whereas all outbound traffic is allowed.

* Changes to security groups take effect immediately.

* You can have any number of EC2 instances within a security group.

* You can have multiple security groups attached to EC2 instances.

* Security groups are STATEFUL, i.e. If you create an inbound rule allowing
  traffic in, that traffic is automatically allowed back out again. On the
  contrary, Network Access Control List is STATELESS.

* You cannot block specific IP addresses using Security Groups, instead use
  Network Access Control List (which will be talked about in the future).

* You can specify allow rules, but not deny rules.

### Volumes & Snapshots

* Volumes exist on EBS. It is basically virtual hard disk.

* Snapshots exist on S3.

* Snapshots are point in time copies of volumes.

* Snapshots are incremental - this means that only the blocks that have changed
  since your last snapshot are moved to S3.

* For the first snapshot, it may take some time to create.

* To create a snapshot for EBS volumes that serve as root devices (bootable),
  you should stop the instance before taking the snapshot.

* However, you can take a snapshot while the instance is running.

* You can create Amazon Machine Images (AMI) from both EBS-backed instances and
  snapshots.

* You can change EBS volume sizes on the fly, including changing the size and
  storage type.

* Volumes will always be in the same availablity zone as the EC2 instance.

* To move an EC2 volume from one AZ/Region to another, take a snap or an image
  of it, then copy it to the new AZ/Region.

* Snapshots of encrypted volumes are encrypted automatically, and volumes
  restored from encrypted snapshots are encrypted automatically.

* Only unencrypted snapshots can be shared. If unecrypted, these snapshots can
  be shared with other AWS accounts or made public.

### EBS vs Instance Store

* All AMIs are categroized as either backed by Amazon EBS or backed by instance
  store.

* For EBS volumes: the root device for an instance launched from the AMI is an
  Amazon EBS volume created from an Amazon EBS snapshot.

* For Instance Store Volume: the root device for an instance launched from the
  AMI is an instance store volume created from a template sotred in Amazon S3.

* Instance Store Volumes are sometimes called Ephemeral Storage.

* Instance Store Volumes cannot be stopped. If the underlying host(hypervisor)
  fails, you will lose your data.

* EBS backed instances can be stopped. You will not lose the data on this
  instance if it is stopped.

* You can reboot both, and you will not lose your data.

* By default, both ROOT volumes will be deleted on termination, however, with
  EBS volumes,  you can tell AWS to keep the root device volume.

### Elastic Load Balancers (ELB)

* Three types of Load Balancers:

    1. Application Load Balancers --> Layer 7
    2. Network Load Balancers --> Layer 4
    3. Classic Load Balancers --> legacy ELB.

* 504 Error means the gateway has timed out. This means that the application is
  not responding within the idle timeout period.

    * Troubleshoot the application. Is it the Web Server or Database Server?

* If you need the IPv4 address of your end user, look for the X-Forwarded-For
  header.

* INstances monitored by ELB are reported as: InService or OutofService.

* Health checks check the instance health by talking to it.

* Have their own DNS name. No IP address is given.

### CloudWatch

* Standard Monitoring = 5 min

* Detailed Monitoring = 1 min. (charge may apply)

* Dashboards - Creates awesome dashboards to see what is happening with your
  AWS envrionment.

* Alarms - Allows you to set alarms that notify you when particular thresholds
  are hit.

* Events - CloudWatch Events helps you to respond to state the changes in your
  AWS resources.

* Logs - CloudWatch Logs helps you to aggregate, monitor, and store logs.

### EC2 Placement Groups

* A clustered placement group can't span multiple Availability Zones.

* A spread placement group can.

* The name you specify for a placement group must be unique within your AWS
  account.

* Only certain types of instances can be launched in a placement group (compute
  optimized, GPU, memory optimized, storage opimized).

* You can't merge placement groups.

* You can't move an existing instance into a placement group. You can create an
  AMI from your existing instance, and then launch a new instance from the AMI
  into the placement group.

### Elastic File System (EFS)

* Supports the Network File System version 4 (NFSv4) protocol.

* It is more flexible then EBS.

* You only pay for the storage you use (no pre-provisioning required).

* Can scale up to the petabytes.

* Can support thousands of concurrent NFS connections.

* Data is stored across multiple AZ's within a region.

* Unlike S3, EFS is block based storage.

* EFS is similar to dropbox. It can be attached to different instances, whereas
  EBS cannot.

### Metadata

* To fetch the instance metadata, use the following:

  ```
  curl http://169.254.169.254/latest/meta-data/
  ```

* To fetch the user metadata, use the following:

  ```
  curl http://169.254.169.254/latest/user-data/
  ```

### Lambda (!!! Important)

* No Servers! Continuous Scaling! Super Cheap!

* Lambda scales out (not up) automatically

* Lambda functions are independent, 1 event = 1 function

* Lambda is serverless

* Know what services are serverless! (EC2 is not serverless)

* Lambda functions can trigger other lambda functions, 1 event can trigger
  multiple functions as well.

* Architectures can get extremely complicated, AWS X-ray allows you to debug
  what is happening.

* Lambda can do things globally, you can use it to back up S3 buckets to other
  S3 buckets etc.

* Know your triggers.
