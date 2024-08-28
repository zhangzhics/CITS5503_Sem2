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

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:
 
[1] Background and basics to Fabric

[2] How to automatically deploy a server using Fabric

### Create an EC2 instance

Use your existing code to create an EC2 instance where you will test your Fabric-based installation.

**NOTE**: Regarding your region name, find it in the table below based on your student number (If you cannot find your region name, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region name.).

| Student Number | Region | Region Name | ami id |
| --- | --- | --- | --- |
| 20666666 – 22980000 | US East (N. Virginia) |	us-east-1 |	ami-0a0e5d9c7acc336f1 |
| 22984000 – 23370000 | Asia Pacific (Tokyo)	| ap-northeast-1	| ami-0162fe8bfebb6ea16 |
| 23400000 – 23798000 | Asia Pacific (Seoul)	| ap-northeast-2	| ami-056a29f2eddc40520 |
| 23799000 – 23863700 | Asia Pacific (Osaka)	| ap-northeast-3	| ami-0a70c5266db4a6202 |
| 23864000 – 23902200 | Asia Pacific (Mumbai)	| ap-south-1	| ami-0c2af51e265bd5e0e |
| 23904000 – 23946000 | Asia Pacific (Singapore)	| ap-southeast-1	| ami-0497a974f8d5dcef8 |
| 23946100 – 24024000 | Asia Pacific (Sydney)	| ap-southeast-2	| ami-0375ab65ee943a2a6 |
| 24025000 – 24071000 | Canada (Central)	| ca-central-1	| ami-048ddca51ab3229ab |
| 24071100 – 24141000 | Europe (Frankfurt)	| eu-central-1	| ami-07652eda1fbad7432 |
| 24143000 – 24700000 | Europe (Stockholm)	| eu-north-1	| ami-07a0715df72e58928 |

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

Rely on the fabric code below to connect to you instance.

```
python3
>>> from fabric import Connection
>>> c = Connection('<your EC2 instance name>')
>>> result = c.run('uname -s')
Linux
>>>
```

### Use Fabric for automation

Write a python script where you first need to automate the setup of a Python 3 virtual environment, nginx and a Django app within the EC2 instance you just created. Then, you should run the Django development server on port 8000 in the background.

From your local OS environment, access the URL: `http://<ip address of your EC2 instance>/polls/`, and output what you've got. 

**NOTE**:  this python script basically needs you to convert instructions (in `Set up an EC2 instance` and `Set up Django inside the created EC2 instance`) in Lab 6 to Fabric commands. The documentation for Fabric is [here](http://docs.fabfile.org/en/2.0/).

Lab Assessment:
	
A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 


