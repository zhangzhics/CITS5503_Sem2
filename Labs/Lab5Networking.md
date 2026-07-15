# Lab 5 - Networking

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

## Background

The aim of this lab is to write a program that will:

\[1] Understand how to configure different network arrangements to gain and control access to computers and other networked resources

\[2] Understand IP addressing and CIDR and the meaning of TCP and UDP ports

## Application Load Balancer

The aim of this part of the lab is to create an application load balancer and load balance requests to 2 EC2 instances.

### \[1] Create 2 EC2 instances

Write a Python Boto3 script to create 2 EC2 instances in two different availability zones (name the instances following the format: `<student number>-vm1` and `<student number>-vm2`) in the region mapped to your student number. In this script, a security group should be created to authorise inbound traffic for HTTP and SSH, which will be used by the following steps.

**NOTE**: Regarding your [region name](Lab1IntroSetup.md#id-3-configure-aws), find it in the table below based on your student number (If you cannot find your region name, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region name.).

### \[2] Create an Application Load Balancer

Create an application load balancer and load balance `HTTP` requests to the created 2 instances. Note that the v2 of the ELB interface below should be used:

```
client = boto3.client('elbv2')
```

The script updates include:

* First, create a load balancer, during which specify the two created region subnets and the security group created in the previous step.
* Second, create a target group using the same VPC that was used to create the instances.
* Third, register targets in the target group.
* Last, create a listener with a default rule Protocol: `HTTP` and `Port 80` forwarding on to the target group.

### \[3] Test the Application Load Balancer

Try to access each EC2 instance using its public IP address in a browser. The load balancer is expected not to work at the moment, because Apache 2 is not installed in the instance. To make it work, follow the steps below:

First, `ssh` to each of the two instances. If you can't make it, try [here](https://bobbyhadz.com/blog/aws-ssh-permission-denied-publickey).

Second, update each instance:

```
sudo apt-get update
```

Third, install Apache2 in each instance:

```
sudo apt install apache2
```

Fourth, edit the `<title>` and `</title>` tags inside the `/var/www/html/index.html` file to show the instance name.

Last, use a browser from your host OS to access each instance by its respective IP address and see if you can get an Apache web page that shows your instance name. Output what you've got. If you are using the University network, you likely cannot access the installed `Apache2`. To address this issue, you may switch to a non-university network.

**NOTE**: Delete all the created AWS resources from the AWS console after the lab is done.

## Lab Assessment

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%).
