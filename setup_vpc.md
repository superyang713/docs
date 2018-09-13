# Setup a custom AWS Virtual Private Cloud (VPC)

Amazon Virtual Private Cloud (VPC) is basically a virtual data center in the
cloud. You have complete control over your virtual networking envrionment,
including selection of your own private IP address range, creation of subnets
and configuration of route tables and network gateways. Benefit of VPC is that
it helps in aspects of cloud computing like privacy, security, and preventing
loss of proprietary data. Here I am going to show you how to set up a VPC in
AWS console.

## Key Component

* Subnets: A subnet can be thought of as dividing a large network into smaller
  networks, which are easier to maintain and can provide security to the
  network from other networks. One subnet corresponds to one availability zone.

* Route Tables: A route table contains a set of rules called routes which
  determine where traffic are directed. You can have multiple route tables in a
  VPC.

* Internet Gateway: It is a combination of hardware and software that provides
  your private networks with a route to the world outside, i.e. the internet
  access. Only one Internet Gateway can be attached to a VPC at a time.

* Security groups: Security groups are a set of firewall rules that controls
  the traffic for your instance. It is the first layer of a VPC security
  defense. It is stateful, and can be associated with multiple instances. You
  cannot create a rule to deny traffic.

* Network Access Control Lists (ACL):It is the second layer of security for
  the VPC that acts as a firewall for controlling traffic in and out of one or
  more subnets. It is stateless. You might set up network ACLs with rules
  similar to your security groups in order to add an additional layer of
  security to your VPC. The default network ACL is configured to allow all
  traffic to flow in and out of the subnets to which it is associated; however,
  when a custom ACL is created, it is default to deny all traffic.


## Goal

I am going to demonstrate how to set up a VPC on AWS console. The configuration
of the VPC is shown below. Basically, there are two subnets, one private and
the other public. A user can access instances in the public subnets via ssh,
whereas the instances in the private subnets can only be accessed through a
bastion server which is hosted in the public subnet. Only outbound internet
traffic is allowed through NAT gateway (or Nat instance in old days) from
private subnet for security reasons.

This VPC configuration is quite common, and useful. For example, the instances
in the public subnets can host webservers, whereas database servers are
protected in private subnets and can only be accessed by the webserver in the
public subnets.

![VPC Diagram](https://www.dropbox.com/s/sjly8txgwtkgg3i/vpc_diagram.png?raw=1)

## Getting Started

If you want to follow along, make sure you have an AWS account. VPC is under
Networking & Content Delivery Section in AWS services, and it does not matter
what region you choose from. Note that in most cases, AWS has already created a
default VPC in each region for your account. In the default VPC, all subnets
are public, meaning any instance you create in that VPC will have internet
access and will be auto-assigned a public IP address. Just bear that in mind.
Ok, so let's get into it.

### Step 1: Create a new VPC

Go to the VPC service main page, and click `Create VPC`. In the popup window,
you can give your new VPC a name, and a IPv4 CIDR range. I chose `10.0.0.0/16`
because it can give me the biggest address range possible for a VPC. If you
don't know what CIDR is, [here](http://cidr.xyz/) is a good website to check
out.

![Create VPC](https://www.dropbox.com/s/u4krd6u2yu93q9c/create_vpc.png?raw=1)

When a new VPC is created, a default Network ACL, a Security Group, and a Route
Table are automatically created as well. What are left to create are Subnets
and Internet Gateways,

### Step 2: Create new Subnets

Go to the `Subnets` section on the left panel and you will notice that there
are already a couple of subnets in there. Those subnets belong to the default
VPC. Now click `Create Subnet`.

It would be better to give a meaningful name for a subnet you are about to
create. The best practice is to give a name that contains information about the
subnet CIDR and availability zone. I named my first subnet `10.0.1.0/24 -
us-east-1a`, which shows the subnet CIDR is `10.0.1.0/24` and the availability
zone is `us-east-1a` for this particular subnet. Also, select the VPC you just
created, and click `create`.

![Create Subnet](https://www.dropbox.com/s/tmzjj07nqeqkpto/create_subnet.png?raw=1)

Note that the available IP address shown in the subnet section is 251, instead
of 256. This is because AWS reserved 5 IP address for specific use. You can
check the official docs [here](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)


### Step 3: Create an Internet Gateway and attach it to one subnet.
