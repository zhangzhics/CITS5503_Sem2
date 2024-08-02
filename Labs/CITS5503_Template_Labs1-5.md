<div style="display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100vh;">

  <h2>Labs 1-5</h2>
  
  <p>Student ID: 23803313</p>
  <p>Student Name: Waleed Khalid Siraj</p>

</div>

# Lab 1

## AWS Account and Log in

### [1] Log into an IAM user account created for you on AWS

First, I went to [AWS Console](https://489389878001.signin.aws.amazon.com/console) and logged in with my student email as username and the password provided.

### [2] Search and open Identity Access Management

1. Clicked on my profile on the top right.
2. Went to **Security Credentials**.
3. Scrolled down to find the **Access Keys** section and clicked on **Create access key**.
4. Selected **CLI**.
   - <Insert screenshot of Access Key Best Practices & Alternatives, hovering on CLI option>
5. Set the description tag.
   - <Insert screenshot of Description Tag page>
6. Clicked on **Create access key**.
   - A confirmation screen popped up confirming that the access key is created.
   - <Insert screenshot of confirmation screen>
7. Made a note of the ID and password or downloaded the CSV file containing the ID and password.

## Set up recent Linux OSes

1. **Download and install VMware for Windows.**
2. **Download Kali Linux for VMware and extract the 7z file.**
3. **Open VMware:**
   - Click on **File** on the top right and select **Open**.
   - Find the VMX file for Kali Linux in the extracted directory.
   - <Insert screenshot>
4. **Edit Virtual Machine Settings:**
   - Set memory to 8GB, 4 processor cores, 30GB hard disk, and NAT network.
   - <Insert screenshot>
5. **Power on the virtual machine.**
6. **Log into Kali Linux with the default ID and password.**

## Install Linux Packages

### [1] Install Python 3.8.x

1. Open terminal and update packages:
   - "sudo apt update"
   - "sudo apt -y upgrade"
   - <Insert screenshot>
2. Check Python version and install pip:
   - "python3 -V"
   - "sudo apt install python3-pip"
   - <Insert screenshot>

### [2] Install awscli

1. Install AWS CLI:
   - "sudo apt install awscli"
2. Upgrade AWS CLI:
   - "pip3 install awscli --upgrade"

### [3] Configure AWS

1. Configure AWS CLI:
   - "aws configure"
   - Enter Access Key ID: "AKIAXD4PI5LY42OCDU4I"
   - Enter Secret Access Key.
   - Default region name: "ap-northeast-3" (as per student ID range).
   - Default output format: "json".

### [4] Install boto3

1. Install boto3:
   - "pip3 install boto3"

## Test the Installed Environment

### [1] Test the AWS environment

1. Test the AWS environment by listing regions:
   - "aws ec2 describe-regions --output table"
   - The output is a table.
   - <Insert screenshot>

### [2] Test the Python environment

1. Test if Python works by extracting the same table in JSON format:
   - "import boto3"
   - "ec2 = boto3.client('ec2')"
   - "response = ec2.describe_regions()"
   - "print(response)"
   - <Insert screenshot>

### [3] Write a Python script

1. **Create a folder on the Desktop named `cloud-lab`.**
2. **Create an empty file and name it `lab1.py`.**
3. **Open the file and add the following Python script, then save:**
   - "import boto3"
   - "import pandas as pd"
   - "from tabulate import tabulate"
   - ""
   - "ec2 = boto3.client('ec2')"
   - "response = ec2.describe_regions()"
   - "regions = response['Regions']"
   - "df = pd.DataFrame(regions, columns=['Endpoint', 'RegionName'])"
   - "print(tabulate(df, headers='keys', tablefmt='psql'))"
4. **Navigate to the folder using the terminal:**
   - "cd /home/kali/Desktop/cloud-lab/"
5. **Make the file executable:**
   - "chmod +x lab1.py"
6. **Execute the Python script:**
   - "python3 lab1.py"
   - <Insert screenshot>

<div style="page-break-after: always;"></div>

# Lab 2

<div style="page-break-after: always;"></div>

# Lab 3

<div style="page-break-after: always;"></div>

# Lab 4

<div style="page-break-after: always;"></div>

# Lab 5

