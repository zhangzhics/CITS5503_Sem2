# Practical Worksheet 2
Version: 1.0 Date: 10/04/2018 Author: David Glance

Date: 21/07/2023 Updated by Zhi Zhang


## Learning Objectives
1. Create an EC2 instance, security group and ssh keys using awscli and python/boto
2. Configure VirtualBox to allow for inbound IP traffic
3. Install and configure Docker and run a hello world application that can be called from the host machine

## Technologies Covered

* Ubuntu
* AWS
* AWS EC2 Python/Boto/awscli/bash scripts VirtualBox
* Docker

Note: please use your Linux VM – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Create an EC2 instance using awscli
### [1] Create a security group

```
aws ec2 create-security-group --group-name <student number>-sg --description "security group for development environment"
```

Note: this will use the default VPC (you will learn about this later in the course) – if you want to specify another VPC, you would use --vpc-id vpc-xxxxxxxx

Note the security group id that is created
Include the code and output with descriptions  in your report.

### [2] Authorise inbound traffic for ssh

```
aws ec2 authorize-security-group-ingress --group-name <student number>-sg --protocol tcp --port 22 --cidr 0.0.0.0/0
```
Include the code and output with descriptions in your report.

### [3] Create a key pair that will allow you to ssh to the EC2 instance

```
aws ec2 create-key-pair --key-name <student number>-key --query 'KeyMaterial' --output text > <student number>-key.pem
```

To use this key on Linux, copy the file to a directory ~/.ssh and change the permissions to:

```
chmod 400 <student number>-key.pem
```
Include the code and the contents in the .pem file with descriptions in your report.
### [4] Create the instance and note the instance id

```
 aws ec2 run-instances --image-id ami-d38a4ab1 --security-group-ids <student number>-sg --count 1 --instance-type t2.micro --key-name <student number>-key --query 'Instances[0].InstanceId'

 ```
Include the code and output with descriptions in your report.
### Optional (unmarked): Add a tag to your Instance
 ```
  aws ec2 create-tags --resources i-??????? --tags Key=Name,Value=<student number>
 ```

// 18.04 ami-176aa375


### [5] Get the public IP address

```
aws ec2 describe-instances --instance-ids i-<instance id from above> --query 'Reservations[0].Instances[0].PublicIpAddress'
```
Include the code and output with descriptions  in your report.
### [6] Connect to the instance
```
ssh -i <student number>-key.pem ubuntu@<IP Address>
```
Include the code and output with descriptions  in your report.
### [7] Look at the instance using the AWS console
Include a screenshot with descriptions in your report.

## Create an EC2 instance with Python Boto script
Include the codes and outputs with descriptions  in your report.

### [1] Repeat the steps (steps 1-5,7 are required, step 6 is optional) above using the equivalent Boto commands in a python script.

You can visit this [page](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html) to get help.

Optional(unmarked): Create an EC2 instance using the console interface. Are there any differences from doing through the command line?

### [2] ****NOTE*****

Once you have finished, log onto the console and terminate the 2 instances you created.
or
```
aws ec2 terminate-instances --instance-ids i-<your instance id>
```

## Using Docker
For some Mac users, you may want to use a Mac terminal to use Docker (using VM still is your best option, using a terminal is an alternative option), please visit this [page](https://docs.docker.com/desktop/install/mac-install/).
### [1] Install Docker
```
sudo apt install docker.io -y
```
Include the code and output with descriptions  in your report.
You may have to

```
sudo systemctl start docker
sudo systemctl enable docker
```
Include the code and output with descriptions  in your report.
### [2] Check the version

```
docker --version
```
Include the code and output with descriptions  in your report.
### [3] Build and run an httpd container

Create a directory called html

Edit a file index.html inside the html directory and add the following content

```
  <html>
    <head> </head>
    <body>
      <p>Hello World!</p>
    </body>
  </html>
```
Include the code and output with descriptions  in your report.
### [4] Create a file called “Dockerfile” outside the html directory with the following content:

```
FROM httpd:2.4
COPY ./html/ /usr/local/apache2/htdocs/
```
Include the code and output with descriptions  in your report.
### [5] Build the docker image

```
docker build -t my-apache2 .
```
Include the code and output with descriptions  in your report.
If you run into permission errors, you may need add your user to the docker group:

```
sudo usermod -a -G docker <username>
```

Be sure to log out and log back in for this change to take effect.

### [6] Run the image

```
docker run -p 80:80 -dit --name my-app my-apache2
```
Include the code and output with descriptions  in your report.
### [7] Open a browser and access address http://localhost or http://127.0.0.1 Confirm you get Hello World!
Include a screenshot for your browser with descriptions in your report.
### [8] Other commands

To check what is running

```
docker ps -a
```
Include the code and output with descriptions  in your report.
To stop and remove the container

```
docker stop my-app
docker rm my-app
```
Include the code and output with descriptions  in your report.

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
