# Practical Worksheet 1 Version: 1.2

Date: 30/07/2020 Author: David Glance

Date: 21/07/2025 Updated by Zhi Zhang and Jichunyang Li

## Learning Objectives

1. Register for an AWS account and explore the user interface
2. Obtain API keys and secret
3. Install a recent VM-based Linux OS, e.g., Ubuntu 22.04 LTS
4. On the Ubuntu 22.04 LTS instance
5. Install AWS CLI on VM
6. Install a virtual environment with Python 3.10
7. Configure the AWSCLI environment with API details and default region
8. Verify awscli is working and write a python boto script to emulate a awscli command

## Technologies Covered

* Ubuntu

* AWS

* Python/Boto/awscli/bash scripts

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

You have several options to set up a Linux environment:

### Windows Users
- Use **WSL2 (Windows Subsystem for Linux)** with **Ubuntu 22.04 LTS** (**Note: Ubuntu 23.04 and above is not suggested. You may meet unexpected errors.**)
- No WSL2 on your machine? Follow this guide: [How to Install WSL2 on Windows 11 (Windows Subsystem for Linux)](https://www.youtube.com/watch?v=eId6K8d0v6o).
- Follow this guide to install an Ubuntu distribution on your machine: [How To Install Ubuntu 22.04 LTS On Windows 11 With WSL2 + RUN Linux GUI Apps](https://www.youtube.com/watch?v=Rzg144v3hfo).

### macOS Users
- Use the built-in **Terminal app** (no extra setup needed for most tasks).
- Run the following command in your Terminal:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
Follow the on-screen instructions. You may be asked to enter your password. If you have not installed Xcode command line tools, you will be prompted to install them. 
- After installing Homebrew, the script will suggest adding Homebrew to your shell profile:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```
- To check if Homebrew is installed correctly, run:
```bash
brew --version
```
You should see the installed version number.

### Other Linux Users
- If you already have a recent Linux distribution (e.g., Ubuntu 20.04 LTS or Kali Linux), you can keep using it.
- Provide relevant screenshots and a brief description for your write-up.

## Install Linux packages

**NOTE**: If you have already installed AWS CLI, Python 3.10, and Python Boto3 library, you don't need to re-install it and instead attach relevant evidence for your write-up.

### [1a] Install Python 3.10 (Windows users only)

The recent Ubuntu versions such as 22.04 LTS have Python 3.10 installed. You should update the packages
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

### [1b] Install Python 3.10 (Mac users only)

Check you Python version
```bash
python3 --version
```

If you have installed Python 3.8 or above, you can skip this step. Otherwise, you can install Python 3.10 using Homebrew.

Install Python 3.10:
```
brew install python@3.10
```

Add the following lines to your shell config file (~/.zshrc, ~/.bash_profile, or ~/.bashrc depending on your shell):
```bash
# Add Python 3.10 to PATH
echo 'export PATH="/opt/homebrew/opt/python@3.10/bin:$PATH"' >> ~/.zshrc

# Set python3 and pip3 aliases
echo 'alias python3="/opt/homebrew/opt/python@3.10/bin/python3.10"' >> ~/.zshrc
echo 'alias pip3="/opt/homebrew/opt/python@3.10/bin/pip3.10"' >> ~/.zshrc
```
For Intel Macs, change `/opt/homebrew/opt/` to `/usr/local/opt/`

Then apply the changes:
```bash
source ~/.zshrc
```

Check the version:
```
python3 --version
```

### [2a] Install awscli (Windows users only)

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

### [2b] Install awscli (Mac users only)

In your Terminal, run the following command to install AWS CLI using Homebrew:

```
brew install awscli
```

The following YouTube video is for your reference.

[Install the AWS CLI on Mac using Homebrew](https://www.youtube.com/watch?v=V5bZsTNz1wY)

### [3] Configure AWS

After typing the command below:

```
aws configure
```
You should see an output like this:

```
AWS Access Key ID [None]: <Your AWS Access Key ID>
AWS Secret Access Key [None]: <Your AWS Secret Access Key>
Default region name [None]: <Your assigned region>
Default output format [None]: json
```

Replace the placeholder values above with your own AWS Access Key, AWS Secret Access Key and default region name.

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

## Lab Assessment

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
