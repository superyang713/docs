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

