# Lab 7 - DevOps

## Learning Objectives

1. Install and configure `Fabric`
2. Deploy a server with nginx installed and configured by `Fabric`
3. Deploy Django code using `Fabric`

## Technologies Covered

* AWS
* Python
* `Fabric`

## Background

The aim of this lab is to write a program that will:

\[1] Background and basics of `Fabric`

\[2] How to automatically deploy a server using `Fabric`

### Create an EC2 instance

Use your existing code to create an EC2 instance where you will test your `Fabric`-based installation.

**NOTE**: Regarding your region, find it in the [region table in Lab 1](Lab1IntroSetup.md#id-3-configure-aws).

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

**NOTE**: After marking, complete the chargeable-resource cleanup described below.

## Live Assessment Checkpoints

Attend your scheduled lab and ask a lab facilitator to check your checkpoints in person. Complete the Fabric deployment before joining the marking queue. Keep the EC2 instance running until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Have the cleanup Console page or command ready before joining the queue. Screenshots and saved output do not replace live results.

Before joining the queue, open your Fabric deployment script and a terminal on your laptop. Open `http://<public-ip>/polls/` in a browser.

### Checkpoint 1: Fabric code and connection — 1 mark

Show that your Fabric script automates these tasks on your EC2 instance:

- Connect to your student EC2 instance.
- Create a Python virtual environment.
- Install or configure nginx.
- Deploy the Django application.
- Start the Django server in the background.

Your directory names may differ from other students' implementations. Run the script's connection function from your laptop and use Fabric to execute:

```text
uname -s
hostname
```

The output must include `Linux` and the hostname of your EC2 instance. The commands must run remotely through Fabric, not directly in your local terminal. You do not need to rerun the complete deployment during marking.

### Checkpoint 2: Deployed result — 1 mark

Refresh:

```text
http://<public-ip>/polls/
```

The Django page must load through nginx on the EC2 instance configured by your Fabric script. The public IP must belong to your student EC2 instance.

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes both checkpoints. Terminate the Lab 7 EC2 instance through the AWS Console or with commands/script. You may keep the Lab 7 key pair and security group.

CLI alternative:

```bash
aws ec2 terminate-instances \
  --instance-ids <lab7-instance-id>
```

Cleanup is complete when the instance is `shutting-down`, `terminated`, or absent from the active instance list. Lab 7 does not require a separate EBS volume or Elastic IP, so these do not need separate checks.
