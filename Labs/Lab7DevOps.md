# Practical Worksheet 7

Version: 1.2 Date: 15/9/2018 Author: David Glance

Date: 28/07/2024 Updated by Zhi Zhang

## Learning Objectives

1. Install and configure Fabric
2. Deploy a server with nginx installed and configured by Fabric
3. Deploy Django code using Fabric

## Technologies Covered

* Ubuntu
* AWS
* Python
* Fabric

## Background

The aim of this lab is to write a program that will:
 
[1] Background and basics to Fabric

[2] How to automatically deploy a server using Fabric

### Create an EC2 instance

Use your existing code to create an EC2 instance where you will test your Fabric-based installation.

**NOTE**: Regarding your region name, find it in the table below based on your student number (If you cannot find your region name, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region name.).

| Student Number | Region | Region Name | ami id |
| --- | --- | --- | --- |
| 21000000 - 23330000 | US East (N. Virginia) |	us-east-1 |	ami-020cba7c55df1f615 |
| 23330001 – 23625120 | Asia Pacific (Tokyo)	| ap-northeast-1	| ami-054400ced365b82a0 |
| 23625121 – 24030000 | Asia Pacific (Seoul)	| ap-northeast-2	| ami-0662f4965dfc70aca |
| 24030001 – 24122100 | US West (Oregon)	| us-west-2	| ami-05f991c49d264708f |
| 24122100 – 24237000 | Asia Pacific (Mumbai)	| ap-south-1	| ami-0f918f7e67a3323f0 |
| 24237001 – 24315000 | Asia Pacific (Singapore)	| ap-southeast-1	| ami-0497a974f8d5dcef8 |
| 24315001 – 24400000 | Asia Pacific (Sydney)	| ap-southeast-2	| ami-010876b9ddd38475e |
| 24400001 – 24497500 | Canada (Central)	| ca-central-1	| ami-0c0a551d0459e9d39 |
| 24497501 – 24590000 | Europe (Frankfurt)	| eu-central-1	| ami-02003f9f0fde924ea |
| 24590001 – 26000000 | Europe (Stockholm)	| eu-north-1	| ami-042b4708b1d05f512 |

### Install and configure Fabric 

The easiest way to install fabric is to:

```
pip install fabric
```

You will need to create a config file in ~/.ssh with the contents:

```
Host <your EC2 instance name>
	Hostname <your EC2 instance public IPv4 DNS>
	User ubuntu
	UserKnownHostsFile /dev/null
	StrictHostKeyChecking no
	PasswordAuthentication no
	IdentityFile <path to your private key>
```

Replace `<your EC2 instance name>` and `<your EC2 instance public IPv4 DNS>` above with your real ones.

Rely on the fabric code below to connect to your instance.

```
python3
>>> from fabric import Connection
>>> c = Connection('<your EC2 instance name>')
>>> result = c.run('uname -s')
Linux
>>>
```

### Use Fabric for automation

Write a Python script where you first need to automate the setup of a Python 3 virtual environment, nginx and a Django app within the EC2 instance you just created. Then, you should run the Django development server on port 8000 in the background.

From your local OS environment, access the URL: `http://<ip address of your EC2 instance>/polls/`, and output what you've got. 

**NOTE**:  This Python script basically needs you to convert instructions (in `Set up an EC2 instance` and `Set up Django inside the created EC2 instance`) in Lab 6 to Fabric commands. The documentation for Fabric is [here](http://docs.fabfile.org/en/2.0/).

**NOTE**: Delete all the created AWS resources from the AWS console after the lab is done.

## Lab Assessment
	
A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 


