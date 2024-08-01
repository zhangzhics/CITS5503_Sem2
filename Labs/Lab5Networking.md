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

Write a Python Boto3 script to create 2 EC2 instances in two availability zones (name the instances following the format: \<student number\>-vm1 and \<student number\>-vm2) in the region mapped to your student number. 

**NOTE**: The created security group must authorise inbound traffic for HTTP and SSH, which will be used by the following steps)

### [2] Create an Application Load Balancer

Update the script below to create an application load balancer and load balance HTTP requests to the created 2 instances. Note that the v2 of the ELB interface below should be used:

```
client = boto3.client('elbv2')
```

The script updates include:

First, create a load balancer, during which specify the two region subnets and the
security group created in the previous step.

Second, create a target group using the same VPC that was used to create
the instances.

Third, register targets in the target group.

Last, create a listener with a default rule Protocol: HTTP and Port 80
forwarding on to the target group.

### [3] Test the Application Load Balancer

Try and access each EC2 instance using its public IP address in a browser. The load balancer is expected not to work at the moment because Apache 2 is not installed in the instance. To make it work, follow the steps below:

First, ssh to each of the two instances. If you can't make it, try [here](https://bobbyhadz.com/blog/aws-ssh-permission-denied-publickey).

Second, update each instance:

```
sudo apt-get update
```

Third, install apache2 in each instance:

```
sudo apt install apache2
```

Fourth, edit the `/var/www/html/index.html` file to show the instance name.

Last, use a browser from your host OS to access each instance by their respective IP address and see if you can get an Apache web page that shows your instance name. Output what you've got. If you are using the University network, it is likely that you cannot access the installed apache2. To address this issue, you may switch to a non-university network.

**NOTE**: Delete all the created AWS resources from AWS console after the lab is done.

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
