# Lab 1 - Introduction & Setup

## Learning Objectives

1. Register for an AWS account and explore the user interface
2. Obtain API keys and secret
3. Install AWS CLI in a Unix-like environment.
4. Configure the AWS CLI environment with API details and default region
5. Verify AWS CLI is working and write a Python boto script to emulate an AWS CLI command

## Technologies Covered

* Unix-like commands
* AWS
* Python/Boto/awscli/bash scripts

## AWS Accounts and Log In

### \[1] Log into an IAM user account created for you on AWS.

Your username is @student.uwa.edu.au. Password details will be provided separately.

The login is here: [https://489389878001.signin.aws.amazon.com/console](https://489389878001.signin.aws.amazon.com/console), where `489389878001` is the account root user id.

{% hint style="warning" %}
You must change your password after logging.
{% endhint %}

Alternatively, you can create your own owner account using your own credit card and utilise free resource tiers on AWS.

### \[2] Search and open Identity Access Management

Click on your user account and click the **Security Credentials tab**: create access key and make a note of the Access key ID and the secret access key (Use case: **Command Line Interface (CLI)**) – you will need these for programmatic access to resources.

NOTE: You should treat the Access key ID and secret carefully. If stolen, these details allow someone to create large numbers of resources and do bad things with the user account. You should bear the incurred costs.

## Set up recent Unix-like OSes

You have the following options to set up a Unix-like environment \[**Choose the ONE best option**]:

### Optrion 1: Windows Users

Use **WSL2 (Windows Subsystem for Linux)** with **Ubuntu 22.04 LTS**. If you do not have WSL2 on your machine, please follow this guide: [How to Install WSL2 on Windows 11 (Windows Subsystem for Linux)](https://www.youtube.com/watch?v=eId6K8d0v6o). After installing WSL2, please follow this guide to install Ubuntu 22.04 LTS: [How To Install Ubuntu 22.04 LTS On Windows 11 With WSL2 + RUN Linux GUI Apps](https://www.youtube.com/watch?v=Rzg144v3hfo).

### Option 3: Apple Silicon MacOS Users

Open your built-in **Terminal** app and run the following command in your Terminal:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Next, follow the on-screen instructions to install the **Homebrew** app. You may be prompted to enter your macOS password. If the Xcode command line tools are not already installed, you'll also be prompted to install them.

After installing Homebrew, run the following commands to add Homebrew into your shell profile:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

To check if Homebrew is installed successfully, run:

```bash
brew --version
```

You should be able to see the installed Homebrew version.

### Option 3: Linux Users

If you're already using a recent Linux distribution (e.g., Ubuntu 20.04 LTS and Ubuntu 22.04 LTS), please continue with it, and attach relevant evidence of this step in your write-up.

NOTE: Ubuntu 23.04, Debian 12 and their newer versions have not been tested, and might lead to unexpected errors when installing Linux packages in the next step.

## Install Linux packages

NOTE: If you have already installed the following packages such as AWS CLI, Python 3.8 or 3.10, and Python Boto3 library, you don't need to re-install it and instead attach relevant evidence of this step for your write-up.

### \[1] Install Python

NOTE: Python 3.8 and 3.10 are recommended.

#### Linux and WSL2 Users

The recent Linux distributions such as Ubuntu 22.04 LTS, have Python 3.10 installed. You should update the packages to obtain the latest version:

```
sudo apt update
sudo apt -y upgrade
```

To check the latest version:

```
python3 -V
```

Next, you need to install `pip3`, which is a tool that allows us to install and manage python libraries.

```
sudo apt install -y python3-pip
```

So you can install python packages by: `pip3 install package_name`

#### Apple Silicon MacOS Users

Check you Python version from your terminal:

```bash
python3 --version
```

If you have installed Python 3.8-3.10, you can skip this step. Otherwise, you can install Python 3.10 using Homebrew as follows:

```
brew install python@3.10
```

Add the following lines to your shell config file (i.e., `~/.zshrc`).

```bash
echo 'export PATH="/opt/homebrew/opt/python@3.10/bin:$PATH"' >> ~/.zshrc

echo 'alias python3="/opt/homebrew/opt/python@3.10/bin/python3.10"' >> ~/.zshrc
echo 'alias pip3="/opt/homebrew/opt/python@3.10/bin/pip3.10"' >> ~/.zshrc
```

Then apply the changes:

```bash
source ~/.zshrc
```

Check the version:

```
python3 --version
```

### \[2] Install awscli

#### Linux and WSL2 Users

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

#### Apple Silicon MacOS Users

Open your Terminal, and run the following command:

```
brew install awscli
```

This guide might be helpful: [Install the AWS CLI on Mac using Homebrew](https://www.youtube.com/watch?v=V5bZsTNz1wY)

### \[3] Configure AWS

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

<table data-search="false"><thead><tr><th>Student Number</th><th>Region</th><th>Region Name</th><th>ami id</th></tr></thead><tbody><tr><td>10000000-24057000</td><td>US East (N. Virginia)</td><td>us-east-1</td><td>ami-0b6d9d3d33ba97d99</td></tr><tr><td>24057001 – 24550000</td><td>Asia Pacific (Tokyo)</td><td>ap-northeast-1</td><td>ami-0126975fb247bf2e7</td></tr><tr><td>24550001 – 24762000</td><td>US West (Oregon)</td><td>us-west-2</td><td>ami-02167eae61967e403</td></tr><tr><td>24762001 – 25063530</td><td>Asia Pacific (Sydney)</td><td>ap-southeast-2</td><td>ami-06259b63260eddc13</td></tr><tr><td>25063531 – 29999999</td><td>Europe (Frankfurt)</td><td>eu-central-1</td><td>ami-0303e2e4a29f041a3</td></tr></tbody></table>

### \[4] Install boto3

```
pip3 install boto3
```

## Test the installed environment

### \[1] Test the AWS environment

```
aws ec2 describe-regions --output table
```

### \[2] Test the python environmen

```
import boto3
ec2 = boto3.client('ec2')
response = ec2.describe_regions()
print(response)
```

This will create an un-tabulated response.

### \[3] Write a Python script

Tabulate the un-tabulated response above to have **2 columns** with **Endpoint** and **RegionName**.

## Lab Assessment

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%).
