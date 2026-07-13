# Lab 7 - DevOps

## Learning Objectives

1. Install and configure `Fabric`
2. Deploy a server with nginx installed and configured by `Fabric`
3. Deploy Django code using `Fabric`

## Technologies Covered

* Ubuntu
* AWS
* Python
* `Fabric`

## Background

The aim of this lab is to write a program that will:

\[1] Background and basics of `Fabric`

\[2] How to automatically deploy a server using `Fabric`

### Create an EC2 instance

Use your existing code to create an EC2 instance where you will test your `Fabric`-based installation.

**NOTE**: Regarding your [region name](Lab1IntroSetup.md#id-3-configure-aws), find it in the table below based on your student number (If you cannot find your region name, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region name.).

### Install and configure `Fabric`

The easiest way to install `fabric` is to:

```
pip install fabric
```

You will need to create a config file in `~/.ssh` with the contents:

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
from fabric import Connection
c = Connection('<your EC2 instance name>')
result = c.run('uname -s')

###Outcome###
Linux
```

### Use `Fabric` for automation

Write a Python script where you first need to automate the setup of a Python 3 virtual environment, nginx and a Django app within the EC2 instance you just created. Then, you should run the Django development server on port 8000 in the background.

From your local OS environment, access the URL: `http://<ip address of your EC2 instance>/polls/`, and output what you've got.

**NOTE**: This Python script basically needs you to convert instructions (in Set up an EC2 instance and Set up Django inside the created EC2 instance) in Lab 6 to Fabric commands. The documentation for Fabric is [here](http://docs.fabfile.org/en/2.0/).

**NOTE**: Delete all the created AWS resources from the AWS console after the lab is done.

## Lab Assessment

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%).
