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

**NOTE**: After marking, complete the chargeable-resource cleanup described below.

## Live Assessment Checkpoints

Attend your scheduled lab and ask a lab facilitator to check your checkpoints in person. Complete both EC2 instances, the Application Load Balancer, and Apache setup before joining the marking queue. Keep the resources running until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Have the cleanup Console pages or commands ready before joining the queue. Screenshots and saved output do not replace live results.

Before joining the queue, open the EC2 and Load Balancer Console pages and the direct and load-balanced web pages.

### Checkpoint 1: EC2 and ALB infrastructure — 1 mark

In the AWS Console or through live AWS output, show:

- `<student-number>-vm1` and `<student-number>-vm2` in different availability zones.
- One security group allowing HTTP and SSH.
- An internet-facing Application Load Balancer in at least two subnets.
- A target group in the same VPC as the instances.
- An HTTP listener on port 80 that forwards to the target group.

Open each instance's direct public IP in the browser. The page or title for each address must identify its backend as `vm1` or `vm2`.

### Checkpoint 2: Working load balancing — 1 mark

Open the target group in the AWS Console and refresh its target list. Both registered targets must be `healthy`.

Then send repeated requests to the ALB DNS name. You may refresh the ALB page in the browser or use:

```bash
ALB_DNS=<load-balancer-dns-name>
for i in {1..10}; do
  curl -s "http://$ALB_DNS" | grep -oE '<title>[^<]+'
done | sort -u
```

The live result must contain both instance names. Direct instance IPs alone do not demonstrate load balancing.

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes both checkpoints. Use the AWS Console or commands/script in this order:

1. Delete the Application Load Balancer.
2. Terminate both EC2 instances.
3. If you created a separate EBS volume or Elastic IP for this lab, delete the unattached volume or release the address.

You may keep the target group, key pair, and security group. Cleanup is complete when the ALB is deleting or absent and both instances are `shutting-down`, `terminated`, or absent from the active instance list.

CLI alternatives:

```bash
aws elbv2 delete-load-balancer \
  --load-balancer-arn <load-balancer-arn>

aws ec2 terminate-instances \
  --instance-ids <vm1-instance-id> <vm2-instance-id>
```
