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
