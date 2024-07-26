# Practical Worksheet 1 Version: 1.2

Date: 30/07/2020 Author: David Glance

Date: 24/07/2024 Updated by Zhi Zhang

## Learning Objectives

1. Register for an AWS account and explore the user interface
2. Obtain API keys and secret
3. Install a recent VM-based Linux OS, e.g., Ubuntu 20.04 LTS
4. On the Ubuntu 20.04 LTS instance
5. Install AWS CLI on VM
6. Install a virtual environment with Python 3.6
7. Configure the AWSCLI environment with API details and default region
8. Verify awscli is working and write a python boto script to emulate a awscli command

## Technologies Covered

* Ubuntu

* AWS

* VirtualBox Python/Boto/awscli/bash scripts

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## AWS Accounts and Log In
### [1] Log into an IAM user account created for you on AWS.

Your username is <student number>@student.uwa.edu.au. Password details will be provided separately.

The login is here: https://489389878001.signin.aws.amazon.com/console, where 489389878001 is the account root user id.

You must change your password after login.

Alternatively, you can create your own owner account using your own credit card and utilise free resource tiers on AWS.

### [2] Search and open Identity Access Management

Click on your user account and click the Security Credentials tab: create access key and make a note of the Access key ID and the secret access key – you will need these for programmatic access to resources.

<div class="alert alert-info" style="font-size:100%">
<b>NOTE:</b> You should treat the Access key ID and secret carefully. If stolen, these details allow someone to create large numbers of resources and do bad things with the user account. You should bear the incurred costs.
</div>

## Set up recent Linux OSes

**NOTE**: You have multiple options for the setup, for example, Windows WSL with recommended Ubuntu OS 20.04 LTS, and Virtual Machine Manager (e.g., VirtualBox, UTM and VMWare) with recommended Ubuntu OS 20.04 LTS. If you have already installed a recent Linux OS, you don't need to re-install it and instead attach appropriate screenshots and description for your write-up (other Linux distributions such as recent Kali Linux should be fine). 

For Windows/MacOS (non-M1) users, please refer to 1.1 in the link below. For M1/M2 etc. MacBook users, please refer to 1.2 in the link.

[https://uwacyber.gitbook.io/cits1003/cits1003-labs/setting-up-your-laptop](https://uwacyber.gitbook.io/cits1003/cits1003-labs/lab-1-setting-up-your-laptop)

For Windows users, WSL is also good and detailed below:

[https://canonical-ubuntu-wsl.readthedocs-hosted.com/en/latest/guides/install-ubuntu-wsl2](https://canonical-ubuntu-wsl.readthedocs-hosted.com/en/latest/guides/install-ubuntu-wsl2)


**Optional:** If you want to run the VirtualBox environment in full screen:

[https://askubuntu.com/questions/1230797/ubuntu-20-04-vm-always-resizes-screen-to-default-size-when-booting](https://askubuntu.com/questions/1230797/ubuntu-20-04-vm-always-resizes-screen-to-default-size-when-booting)

**Optional:** If you are using VirtualBox and have any issues with copy-paste from your host machine to your VM, you should enable clipboard copying from the Devices menu of VirtualBox (Settings > General > Advance > Shared Clipboard > Bidirectional). You will need to run and install the VirtualBox Guest Additions first from the same menu (On Ubuntu 20.04, you should install build tools first `sudo apt install linux-headers-$(uname -r) build-essential dkms -y`

## Install Linux packages

**NOTE**: If you have already installed AWS CLI, Python 3.8.x, and Python Boto3 library, you don't need to re-install it and instead attach relevant evidence for your write-up.

### [1] Install Python 3.8.x

The recent Ubuntu versions such as 20.04 LTS have Python 3.8.x installed. You should update the packages
to obtain the latest version:

```
sudo apt update
sudo apt -y upgrade
```

To check the latest version:
```
python3 -V
```

Now we need to install `pip3`, which is a tool that will allow us to install and manage python libraries.
```
sudo apt install -y python3-pip
```

Python packages can be installed by: `pip3 install package_name`


### [2] Install awscli

```
sudo apt install awscli
```

Then, 

```
pip3 install awscli --upgrade
```

Alternatively:

```
sudo snap install aws-cli --classic
```

### [3] Configure AWS

After typing the command below:

```
aws configure
```
You should see an output like this:

```
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: ap-southeast-2
Default output format [None]: json
```

Replace the placeholder values above with your own AWS Access Key, AWS Secret Access Key and region code.

**NOTE**: Regarding your region code, find it in the table below based on your student number (If you cannot find your region code, it means you enrolled late and you should send an email to `cits5503-pmc@uwa.edu.au` requesting your region code.).

| Student Number | Region Name | Region Code | ami id |
| --- | --- | --- | --- |
| 20666666 – 22985000 | US East (N. Virginia) |	us-east-1 |	ami-0a0e5d9c7acc336f1 |
| 22985001 – 23370000 | Asia Pacific (Tokyo)	| ap-northeast-1	| ami-0162fe8bfebb6ea16 |
| 23370001 – 23798000 | Asia Pacific (Seoul)	| ap-northeast-2	| ami-056a29f2eddc40520 |
| 23798001 – 23863200 | Asia Pacific (Osaka)	| ap-northeast-3	| ami-0a70c5266db4a6202 |
| 23863201 – 23898000 | Asia Pacific (Mumbai)	| ap-south-1	| ami-0c2af51e265bd5e0e |
| 23898001 – 23952000 | Asia Pacific (Singapore)	| ap-southeast-1	| ami-0497a974f8d5dcef8 |
| 23952001 – 24030000 | Asia Pacific (Sydney)	| ap-southeast-2	| ami-0375ab65ee943a2a6 |
| 24030001 – 24074000 | Canada (Central)	| ca-central-1	| ami-048ddca51ab3229ab |
| 24074001 – 24147000 | Europe (Frankfurt)	| eu-central-1	| ami-07652eda1fbad7432 |
| 24147001 – 24700000 | Europe (Stockholm)	| eu-north-1	| ami-07a0715df72e58928 |


### [4] Install boto3

```
pip3 install boto3
```

## Test the installed environment

### [1] Test the AWS environment

```
aws ec2 describe-regions --output table
```

### [2] Test the python environment

```
python3
>>> import boto3
>>> ec2 = boto3.client('ec2')
>>> response = ec2.describe_regions()
>>> print(response)
```

This will create an un-tabulated response.

### [3] Write a Python script

Tabulate the un-tabulated response above to have 2 columns with Endpoint and RegionName.

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 

