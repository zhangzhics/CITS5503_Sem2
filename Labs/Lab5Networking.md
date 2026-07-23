# Lab 5 - Networking

## Learning Objectives

1. Networking and NAT
2. Setting up an Application Load Balancer

Technologies Covered

* AWS ELB
* Networking
* NAT
* Python/Boto scripts

## Background

The aim of this lab is to write a program that will:

\[1] Understand how to configure different network arrangements to gain and control access to computers and other networked resources

\[2] Understand IP addressing and CIDR and the meaning of TCP and UDP ports

## Application Load Balancer

The aim of this part of the lab is to create an application load balancer and load balance requests to 2 EC2 instances.

### \[1] Create 2 EC2 instances

Write a Python Boto3 script to create 2 EC2 instances in two different availability zones (name the instances following the format: `<student number>-vm1` and `<student number>-vm2`) in the region mapped to your student number. In this script, a security group should be created to authorise inbound traffic for HTTP and SSH, which will be used by the following steps.

**NOTE**: Regarding your region, find it in the [region table in Lab 1](Lab1IntroSetup.md#id-3-configure-aws).

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


## Live Lab Assessment (2 marks)

Make sure you have practiced and completed all the required checkpoints before joining the marking queue. When you are next in line, have your commands/scripts/cleanup ready so that you can demonstrate each checkpoint live to a lab facilitator for verification. 

You have only ONE attempt to demonstrate each checkpoint in front of the lab facilitator. If you fail any checkpoint, the corresponding marks will not be awarded. Screenshots, copied output, and prerecorded demonstrations are not accepted.

### Checkpoint 1: EC2 and ALB (1 mark)

From your AWS Management Console, show:

- Two running EC2 instances follow the naming format of `<student-number>-vm1` and `<student-number>-vm2` and are in two different availability zones.
- The EC2 instances are both t3.micro.
- A running internet-facing Application Load Balancer operates in two subnets.


### Checkpoint 2: Workload balancing (0.5 mark)

From your browser, access each EC2 instance via their public IP and show that the page title displays each instance name.

### Checkpoint 3: Cleanup (0.5 mark)

After the first two checkpoints are completed, open AWS Management Console and complete the following actions in order:

1. Delete load balancers you created.
2. Delete/Terminate instances you created.


