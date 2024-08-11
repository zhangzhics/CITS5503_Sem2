# Practical Worksheet 7

Version: 1.2 Date: 15/9/2018 Author: David Glance

## Learning Objectives

1. Install and configure Fabric
2. Deploy a server with nginx installed and configured by Fabric
3. Deploy Django code using Fabric

## Technologies Covered

* Ubuntu
* AWS
* Python
* Fabric

Note: please use your Linux VM – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:
 
[1] Background and basics to Fabric
[2] How to automatically deploy a server using Fabric

### [Step 1] Create an EC2 instance

Use your existing code to create an EC2 instance that you will test
your Fabric-based installation on.

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

### [Step 2] Install and configure Fabric on your VM

NOTE do this on your VirtualBox VM

The easiest way to install fabric is to:

```
pip install fabric
```

You will need to create a config file in ~/.ssh with the contents:

```
Host <ec2instance>
	Hostname <EC2 instance public DNS>
	User ubuntu
	UserKnownHostsFile /dev/null
	StrictHostKeyChecking no
	PasswordAuthentication no
	IdentityFile <path to your private key>
```

Use ssh <file name>  to connect to you instance


Remember to replace <ec2instance> with your EC2 name you used in the
configuration - use your student number to identify resources.


You can test fabric from the command line:

```
python
>>> from fabric import Connection
>>> c = Connection(‘<ec2instance>’)
>>> result = c.run(‘uname -s’)
Linux
>>>
```

### [Step 3] Write a python script to automate the installation of nginx

Write a python script using fabric to set up and configure nginx as
you did for the Django app last week

This will consist of doing the same commands you would do manually to
configure nginx but using the commands:

sudo("commands go here separated by ;")

and/or

run("Commands go here separated by ;")

## [Step 4] Update the python script to install your Django app

You will copy the files from your local directory to the new EC2 instance. You may find this easier if you create a tar/zip file first of the code and then untar/unzip on the EC2 machine.

Add the necessary commands to configure the virtual environment and
clone your Django app from your local directory - this is basically taking the
instructions you used in that lab and converting them to Fabric commands.

The final command should be the command to run the server - if you add
an '&' at the end it will run the process in the background. Note -
you would normally use Gunicorn to do this not manage.py runserver but
we are keeping it simple.

The documentation for Fabric is here: http://docs.fabfile.org/en/2.0/

Lab Assessment:
	
A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 


