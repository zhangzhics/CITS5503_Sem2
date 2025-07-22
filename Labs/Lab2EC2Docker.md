# Practical Worksheet 2
Version: 1.0 Date: 10/04/2018 Author: David Glance

Date: 21/07/2025 Updated by Zhi Zhang and Jichunyang Li


## Learning Objectives
1. Create an EC2 instance, security group and ssh keys using awscli and python/boto
2. Install and configure Docker and run a hello world application that can be called from the host machine

## Technologies Covered

* Ubuntu
* AWS
* AWS EC2 Python/Boto/awscli/bash scripts
* Docker

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Create an EC2 instance using awscli
### [1] Create a security group

```
aws ec2 create-security-group --group-name <student number>-sg --description "security group for development environment"
```

This will use the default VPC (if you want to specify a VPC, use `--vpc-id vpc-xxxxxxxx`). Take a note of the security group id that is created. 

### [2] Authorise inbound traffic for ssh

```
aws ec2 authorize-security-group-ingress --group-name <student number>-sg --protocol tcp --port 22 --cidr 0.0.0.0/0
```

### [3] Create a key pair

```
aws ec2 create-key-pair --key-name <student number>-key --query 'KeyMaterial' --output text > <student number>-key.pem
```

To use this key on Linux, copy the file to a directory `~/.ssh` and change the permissions to:

```
chmod 400 <student number>-key.pem
```
### [4] Create the instance 

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


Based on your region code, find the corresponding ami id in the table above and fill it in the command below:

```
 aws ec2 run-instances --image-id <ami id> --security-group-ids <student number>-sg --count 1 --instance-type t3.micro --key-name <student number>-key --query 'Instances[0].InstanceId'

 ```

### [5] Add a tag to your Instance

 ```
  aws ec2 create-tags --resources <Instance Id from above> --tags Key=Name,Value=<student number>
 ```
**NOTE**: If you need to create a single instance, follow the naming format of `<student number>-vm` (e.g., 24242424-vm). If you need to create multiple ones, follow the naming format of `<student number>-vm1` and `<student number>-vm2` (e.g., 24242424-vm1, 24242424-vm2).

### [6] Get the public IP address

```
aws ec2 describe-instances --instance-ids <Instance Id from above> --query 'Reservations[0].Instances[0].PublicIpAddress'
```

### [7] Connect to the instance via ssh
```
ssh -i <student number>-key.pem ubuntu@<IP Address from above>
```

### [8] List the created instance using the AWS console

## Create an EC2 instance with Python Boto3

Use a Python script to implement the steps above (steps 1-6 are required, repeat step 8 using the AWS console again; Step 7 is optional). Refer to [page](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html) for details. **Note**: Remember that your security group name, key pair name, and instance name should be different from those used in **Create an EC2 instance using awscli**.

## Use Docker inside a Linux OS

### macOS Users

- Go to the official Docker website: [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop//)
- Choose the version for your chip type(Apple Silicon/Intel) and Download the .dmg file
- Launch Docker from Applications
- Open Terminal and run the following command to check if Docker is running:
      
```bash
docker --version
```
### Linux/WSL2 Users

#### [1] Install Docker
```
sudo apt install docker.io -y
```

#### [2] Start Docker
```
sudo systemctl start docker
```

#### [3] Enable Docker
```
sudo systemctl enable docker
```

#### [4] Check the version

```
docker --version
```

#### [5] Build and run an httpd container

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

Create a file called Dockerfile outside the html directory with the following content:

```
FROM httpd:2.4
COPY ./html/ /usr/local/apache2/htdocs/
```

Build a docker image

```
docker build -t my-apache2 .
```

If you run into permission errors, you may need add your user to the docker group:

```
sudo usermod -a -G docker <username>
```

Be sure to log out and log back in for this change to take effect.

Run the image

```
docker run -p 80:80 -dit --name my-app my-apache2
```

Open a browser and access address: http://localhost or http://127.0.0.1. 

Confirm you get "Hello World!"

#### [6] Other docker commands

To check what is running

```
docker ps -a
```
To stop and remove the container

```
docker stop my-app
docker rm my-app
```

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
