# Practical Worksheet 5

Version: 1.1 Date: 28/08/2018 Author: David Glance

Date: 21/07/2023 Updated by Zhi Zhang

## Learning Objectives

1. Networking and NAT
2. Setting up an Application Load Balancer

Technologies Covered

* Ubuntu
* AWS
* AWS ELB
* Networking
* NAT
* Python/Boto scripts
* VirtualBox

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:

[1] Understand how to configure different network arrangements to gain and control access to computers and other networked resources

[2] Understand IP addressing and CIDR and the meaning of TCP and UDP ports

## Networking

**Optional**: Configure inbound IP on your VM (Unmarked)

This can be done in a number of ways, but we are going to use NAT port mapping. When a VM is created, it defaults to creating a single NAT interface. Here, VirtualBox is used as an example.

[1] Turn off a specific VM you want to configure.

[2] In the VirtualBox Manager, select the VM, click `Settings` and then `Network`. Choose Adapter 1 that should have been configured as NAT. Click on `Advanced` and then `Port Forwarding`. Set up 1 rule:
   Use the host IP 127.0.0.1 and host port 2222 and map that to Guest Port 22

[3] You can test the NAT'd port by seeing if you can access it from your host OS. Enable SSH to the VM by installing **sshd** as follows:

```
sudo apt install tasksel
sudo tasksel install openssh-server
```

start the ssh service by:

```
sudo service ssh start
```

you can stop it using:

```
 sudo service ssh stop
 ```

To SSH to the VM, open a terminal on your host OS (or use Putty from Windows) and SSH as

```
ssh -p 2222 <usermame>@127.0.0.1
```

You should be prompted for your password

## Application Load Balancer

The aim of this part of the lab is to create an application load balancer and load balance requests to 2 EC2 instances. 

### [1] Create 2 EC2 instances

Write a Python Boto3 script to create 2 EC2 instances (the instance type can be `t2.micro` or `t3.micro`) in two different availability zones (name the instances following the format: \<student number\>-vm1 and \<student number\>-vm2) in the region mapped to your student number. In this script, a security group should be created to authorise inbound traffic for HTTP and SSH, which will be used by the following steps. 

**NOTE**: Regarding your region name, find it in the table below based on your student number (If you cannot find your region name, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region name.).

| Student Number | Region | Region Name | ami id |
| --- | --- | --- | --- |
| 21000000 - 23330000 | US East (N. Virginia) |	us-east-1 |	ami-020cba7c55df1f615 |
| 23330001 – 23625120 | Asia Pacific (Tokyo)	| ap-northeast-1	| ami-054400ced365b82a0 |
| 23625121 – 24030000 | Asia Pacific (Seoul)	| ap-northeast-2	| ami-0662f4965dfc70aca |
| 24030001 – 24122100 | US West (Oregon)	| us-west-2	| ami-05f991c49d264708f |
| 24122100 – 24237000 | Asia Pacific (Mumbai)	| ap-south-1	| ami-0f918f7e67a3323f0 |
| 24237001 – 24315000 | Asia Pacific (Singapore)	| ap-southeast-1	| ami-0497a974f8d5dcef8 |
| 24315001 – 24400000 | Asia Pacific (Sydney)	| ap-southeast-2	| ami-02c7683e4ca3ebf58 |
| 24400001 – 24497500 | Canada (Central)	| ca-central-1	| ami-0c0a551d0459e9d39 |
| 24497501 – 24590000 | Europe (Frankfurt)	| eu-central-1	| ami-02003f9f0fde924ea |
| 24590001 – 26000000 | Europe (Stockholm)	| eu-north-1	| ami-042b4708b1d05f512 |

### [2] Create an Application Load Balancer

Update the script above to create an application load balancer and load balance HTTP requests to the created 2 instances. Note that the v2 of the ELB interface below should be used:

```
client = boto3.client('elbv2')
```

The script updates include:

First, create a load balancer, during which specify the two created region subnets and the
security group created in the previous step.

Second, create a target group using the same VPC that was used to create
the instances.

Third, register targets in the target group.

Last, create a listener with a default rule Protocol: HTTP and Port 80
forwarding on to the target group.

### [3] Test the Application Load Balancer

Try and access each EC2 instance using its public IP address in a browser. The load balancer is expected not to work at the moment, because Apache 2 is not installed in the instance. To make it work, follow the steps below:

First, ssh to each of the two instances. If you can't make it, try [here](https://bobbyhadz.com/blog/aws-ssh-permission-denied-publickey).

Second, update each instance:

```
sudo apt-get update
```

Third, install apache2 in each instance:

```
sudo apt install apache2
```

Fourth, edit the `<title>` and `</title>` tags inside the `/var/www/html/index.html` file to show the instance name.

Last, use a browser from your host OS to access each instance by their respective IP address and see if you can get an Apache web page that shows your instance name. Output what you've got. If you are using the University network, it is likely that you cannot access the installed apache2. To address this issue, you may switch to a non-university network.

**NOTE**: Delete all the created AWS resources from AWS console after the lab is done.

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
