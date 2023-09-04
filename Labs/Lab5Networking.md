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

Note: please use your Linux VM – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:

[1] Understand how to configure different network arrangements to gain and control access to computers and other networked resources

[2] Understand IP addressing and CIDR and the meaning of TCP and UDP ports

## Networking

### [Step 1] Configure inbound IP on your VM (Optional)

This can be done in a number of ways, but we are going to use NAT port mapping. When a VM is created, it defaults to creating a single NAT interface. Here, VirtualBox is used as an example.

[1] Turn off a specific VM you want to configure.

[2] In the VirtualBox Manager, select the VM, click Settings and then Network. Choose Adapter 1 that should have been configured as NAT. Click on Advanced and then Port Forwarding. Set up 1 rule:
   Use the host IP 127.0.0.1 and host port 2222 and map that to Guest Port 22

[3] You can test the NAT'd port by seeing if you can access it from your hostOS. Enable ssh to the VM by installing **sshd** as follows:

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

To ssh to the VM, open a terminal on your hostOS (or use Putty) and ssh as

```
ssh -p 2222 <usermame>@127.0.0.1
```

You should be prompted for your password

### [Step 2] Setting up an Application Load Balancer

The aim of this part of the lab is to create an application load
balancer and load balance requests to 2 EC2 instances. As there is a
restriction of only 20 load balancers per region, we are going to
relax the requirement to create instances and the ELB in the
ap-southeast-2 region.

Before running the application to create a load balancer and
instances, check how many are running in a particular region. Select a
region that has capacity.


### USE YOUR STUDENT NUMBER TO IDENTIFY ALL RESOURCES

Objective: Write a Boto3 application to create 2 EC2 instances in two availability zones (name the instances following the format: \<student number\>-\<availability zone name\>), create an application load balancer and load balance HTTP requests to the 2 instances. Inside the two instances, you will need to get Apache 2 installed and a file of index.html edited. Do this after you have created the instances and ALB. In a future lab you will learn how this could be done through a program as well.

[1] Create 2 EC2 instances in two
different availability zones of a specific region. 

Note: You will need to use v2 of the ELB interface:

```
client = boto3.client('elbv2')
```

[2] Create the Application Load Balancer.

The steps involved in this are:

[a] Create the load balancer and specify the two region subnets and a
security group (note that the security group should authorise inbound traffic for HTTP, which is used by the following step [d])

[b] Create a target group using the same VPC that you used to create
the instances

[c] Register targets in the target group

[d] Create a listener with a default rule Protocol: HTTP and Port 80
forwarding on to the target group

Try and access the EC2 instance using the public IP address of the load balancer in a browser. The load balancer will not be working at this point because Apache 2 is not installed. Then, on each instance, install apache2:

```
sudo apt install apache2
```

Edit the /var/www/html/index.html file to report the instance name

Now verify that the load balancer is working: use a browser from your hostOS to access both of the EC2 instances by refreshing the web page. Note that if you are using the Uni's network, it is likely that you cannot access the installed apache2. To address this issue, you have to switch to a non-uni network, e.g., using your mobile data as a personal hotspot.


*IMPORTANT* When finished. Delete the Load balancer, target group,
listener and EC2 instances.

Lab Assessment:

A structured description (15%). A clear step-by-step with detailed descriptions (85%). 
