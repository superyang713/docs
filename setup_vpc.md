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

Go to the `Internet Gateways` section on the left panel and click `Create
internet gateway`. Similar to the Subnets main page, if this is the first time
you get into this page, you can see that there is already an Internet Gateway
with the state of "attached" in the list. This Internet Gateway is actually
attached to the default VPC. Since one Internet Gateway can only be attached to
one VPC, you need to create a new one for your custom VPC. Click the `Create
internet gateway` button, give it a name like "MyIGW", and hit `Create`.

Now you will see that a new Internet Gateway appeared in the list with the
state of "detached". To attach it to the custom VPC, click the `Action` button,
and choose `Attach to VPC`. Inside the configuration page, you will notice that
the internet gateway can only be attached to the custom VPC, and the default
VPC is not in the option list, which indicates that an internet gateway and a
VPC is one-to-one relationship. Now click `Attach`, and your VPC now has
internet access. It is just that simple.

![Internet Gateway](https://www.dropbox.com/s/fhah7lg4aaiinsy/internet_gateway.png?raw=1)

### Step 4: Configure Route Table.

Now that the custom VPC has an internet gateway attached, you need to configure
the route table so that the public subnet can be routed to the internet
gateway.

Go to the Route Table panel, and you can see that there are two route tables in
the list, which are the main (or default) route table for the default VPC and
the newly created custom VPC, respectively. Choose the route table for the
custom VPC, and click `Routes`, you can see there is already one rule in there,
which is to allow all subnets to communicate with each other by default.

Now go to the `Subnet Association` in the lower section, you can
see a message,

> You do not have any subnet associations.

> The following subnets
have not been explicitly associated with any route tables and are therefore
associated with the main route table.

which means that every single time when a new subnet is created, it will always
be associated with the main route table by default. so for this very reason,
it is not a good practice to let the main route table have a way out to the
internet, because if so, every time a new subnet is provisioned, it will be
internet accessible.

So, let's create a new route table under the custom VPC shown below. After
that, a new route table will be shown in the list.

![Route Table](https://www.dropbox.com/s/6z8ysl9u9hgwdzz/create_route_table.png?raw=1)

Next, you need to enable the internet access. To do this, go to the `Routes`
section in the lower section, as shown below. Add a new rule to make all the
subnets associated with this route table public.

![Configure route table](https://www.dropbox.com/s/t2gnsfds1hlw9sw/config_route_table.png?raw=1)

What's left to do now is to associate one subnet in our VPC to this route table
to make it public. You can easily do this in `Subnet Associations`.

### Step 5 (Optional): Enable auto-assign IP addresses.

Since we have a public subnet with internet access now, public ip address for
EC2 instance created inside the public subnet will be very important. By
default, no ip address will be assigned to the EC2 instances in the public
subnet, and we need to change it. Now go to `Subnets` panel, and click
`Action`. In the dropdown menu, click `Modify auto-assign IP settings`. You
can enable IP auto-assignement there.

![Auto-assign IP](https://www.dropbox.com/s/7kb52qc28b26ccw/auto-assign.png?raw=1)

### Step 6: Create a new security group for EC2 inside the custom VPC.

Security group cannot span different VPCs. If you have security groups in
your default VPC, you cannot use them in your newly created custom VPC. You can
enable any port that fits your needs, but for me, I am just going to enable
SSH, HTTP, and HTTPS ports, and call this security group `Web-DMZ`.

![Auto-assign IP](https://www.dropbox.com/s/7a2z0a5qp4yq1k8/security_group.png?raw=1)

Now create an EC2 instance in the public subnet and private subnet,
respectively, and attach the `Web-DMZ` security group to the public instance.
Now you can access into your public instance via ssh. For security reason, in
order to access your private instance, you should use a bastion host, which is
a hardened instance that you can create from AWS Nat AMI. For more information,
please check out [this article](https://aws.amazon.com/blogs/security/securely-connect-to-linux-instances-running-in-a-private-amazon-vpc/).

Another problem about the current setup for the private instance is that it
does not have any internet access. What if you need to run security updates or
install new applications on the private instance? You can do this by using
Network Address Translation(NAT) instances or NAT Gateways.

### Step 7: Set up NAT Gateways.

NAT instance is on the way out, and NAT gateway is the new service offered by
AWS. NAT gateway is more reliable and much easier to manage than NAT instance.
The whole idea of NAT gateway is to enable instances in a private subnet to
connect to the internet or other AWs services, but prevent the internet from
initiating a connection with thoes instances.

In oder to create a NAT gateway, Go to the `NAT Gateway` section under VPC,
and click `Create NAT Gateway`. Make sure that the NAT gateway is created
inside the public subnet. It is going to take about 5 to 10 minutes for
the NAT Gateway to be actually provisioned.

### Step 8: Add NAT Gateway to the Route Table.

After the NAT Gateway is provisioned, go to the Route Tables, and configure the
main route table, which the private subnet is associated with. The new rule is
to allow traffic from all instances inside the private subnet to be routed to
the NAT Gateway. By doing so, all the private instances will have internet
access. In case you wonder what is the difference between NAT instance and NAT
gateway, you can check it out [here](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html),
but definitely the NAT Gateway is recommended.

One thing to note here is that NAT Gateway is used to provide safe internet
access for instances inside a private subnet, whereas a bastion host inside a
public subnet is recommended to use for SSH access into the private instances.

![NAT Gateway](https://www.dropbox.com/s/lf7r1oq3ybsjtpz/nat_gateway.png?raw=1)

### Step 9: Add new ACL to protect your subnets.

ACL is the second layer of security for the VPC that acts as a firewall for
controlling traffic in and out of one or more subnets. It is stateless. You
might set up network ACLs with rules similar to your security groups in order
to add an additional layer of security to your VPC. The default network ACL is
configured to allow all traffic to flow in and out of the subnets to which it
is associated; however, when a custom ACL is created, it is default to deny
all traffic.

Key thing to note here is that one subnet can only be associated with one ACL,
whereas one ACL can be associated with multiple subnets. It is a one-to-many
relationship.
