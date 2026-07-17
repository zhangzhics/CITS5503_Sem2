# Lab 2 - EC2 & Docker

## Learning Objectives

1. Create an EC2 instance, security group and ssh keys using awscli and python/boto
2. Install and configure Docker and run a hello world application that can be called from the host machine

## Technologies Covered

* AWS
* AWS EC2 Python/Boto/awscli/bash scripts
* Docker

## Create an EC2 instance using awscli

### \[1] Create a security group

```
aws ec2 create-security-group --group-name <student number>-sg --description "security group for development environment"
```

This will use the default VPC (if you want to specify a VPC, use `--vpc-id vpc-xxxxxxxx`). Take a note of the security group id that is created.

### \[2] Authorise inbound traffic for ssh

```
aws ec2 authorize-security-group-ingress --group-name <student number>-sg --protocol tcp --port 22 --cidr 0.0.0.0/0
```

### \[3] Create a key pair

```
aws ec2 create-key-pair --key-name <student number>-key --query 'KeyMaterial' --output text > <student number>-key.pem
```

To use this key, copy the file to a directory `~/.ssh` and change the permissions to:

```
chmod 400 <student number>-key.pem
```

### \[4] Create the instance

Based on the [region table in Lab 1](Lab1IntroSetup.md#id-3-configure-aws), find your corresponding ami id and fill it in the command below:

```
 aws ec2 run-instances --image-id <ami id> --security-group-ids <student number>-sg --count 1 --instance-type t3.micro --key-name <student number>-key --query 'Instances[0].InstanceId'

```

### \[5] Add a tag to your Instance

```
 aws ec2 create-tags --resources <Instance Id from above> --tags Key=Name,Value=<Instance Name>
```

**NOTE**: If you create a single instance, you must name it using the format of `<student number>-vm` (e.g., 24242424-vm). If you need to create multiple ones, follow the naming format of `<student number>-vm1` and `<student number>-vm2` (e.g., 24242424-vm1, 24242424-vm2).

### \[6] Get the public IP address

```
aws ec2 describe-instances --instance-ids <Instance Id from above> --query 'Reservations[0].Instances[0].PublicIpAddress'
```

### \[7] Connect to the instance via ssh

```
ssh -i <student number>-key.pem ubuntu@<IP Address from above>
```

### \[8] List the created instance using the AWS console

## Create an EC2 instance with Python Boto3

Use a Python script to implement the steps above (steps 1-6 are required, repeat step 8 using the AWS console again; Step 7 is optional). Refer to [page](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html) for details. **NOTE**: Remember that your security group name, key pair name, and instance name should be different from those used in **Create an EC2 instance using awscli**.

## Install Docker

### Apple Silicon MacOS Users

#### Install Docker

Run the following command in your terminal to install Docker:

```bash
brew install --cask docker
```

If you have difficulty installing Docker with HomeBrew, you can install Docker manually.

Go to the official Docker website: [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/), choose the version for your processor type (e.g., Apple Silicon), and download the `.dmg` file. Then Open Docker from Applications.

#### Check the version

Open the terminal and run the following command:

```bash
docker --version
```

You should see the Docker version information.

### Linux and WSL2 Users

#### \[1] Install Docker

```
sudo apt install docker.io -y
```

#### \[2] Start Docker

```
sudo systemctl start docker
```

#### \[3] Enable Docker

```
sudo systemctl enable docker
```

#### \[4] Check the version

```
docker --version
```

## Build and run an httpd container

Create a directory called html.

Edit a file index.html inside the html directory and add the following content:

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

Build a docker image.

```
docker build -t my-apache2 .
```

If you run into permission errors, you may need add your user to the docker group:

```
sudo usermod -a -G docker <username>
```

Make sure to log out and log back in for this change to take effect.

Run the image

```
docker run -p 80:80 -dit --name my-app my-apache2
```

Open a browser and access address: http://localhost or http://127.0.0.1.

Confirm you get "Hello World!"

List all Docker containers, including both running and stopped ones:

```
docker ps -a
```

Stop the running container:

```
docker stop my-app
```

Then, remove the stopped container:

```
docker rm my-app
```

**NOTE**: Delete the created EC2 instance(s) from AWS console after the lab is done.

## Live Assessment Checkpoints

Attend your scheduled lab and ask a lab facilitator to check your checkpoints in person. Complete all instance creation and Docker work before joining the marking queue. Keep both EC2 instances running until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Open the relevant EC2 Console cleanup pages before joining the queue. Screenshots and saved output do not replace live results.

Before joining the queue, prepare:

- The AWS CLI-created EC2 instance, security group, and key pair.
- The separately named Boto3-created EC2 instance, security group, and key pair.
- Your Boto3 script.
- A working SSH session or SSH command for the CLI-created instance.
- The running local Docker container and `http://127.0.0.1` open in a browser.

### Checkpoint 1: AWS CLI EC2 resources and SSH — 0.5 marks

In the AWS Console or through live AWS CLI output, show:

- Your student-named EC2 instance.
- Your student-named security group allowing TCP port 22.
- Your student-named key pair.
- The instance's public IP address.

Then run:

```bash
ssh -i <key-file.pem> ubuntu@<public-ip> "hostname && uname -s"
```

An already-open SSH window is also acceptable. Run `hostname` and `uname -s` in that window. The output must identify the remote instance and include `Linux`.

### Checkpoint 2: Boto3-created EC2 resources — 0.5 marks

Open your Boto3 script and show the code that creates the instance, security group, key pair, and Name tag. Do not rerun a creation-only script during marking. Refresh the AWS Console and show the existing Boto3-created resources. Their names must include your student number and differ from the Checkpoint 1 names.

### Checkpoint 3: Docker result — 1 mark

Run:

```bash
docker ps
```

Show a running `my-app` container, or your published student-named equivalent, with host port 80 mapped to container port 80. Refresh `http://127.0.0.1` in your browser. The page must display `Hello World!`.

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes all three checkpoints. Open AWS Console → EC2 and complete these actions:

1. Open **Instances**, select both Lab 2 instances, then choose **Instance state → Terminate instance**. Do not leave an instance in `Running` or `Stopped`.
2. If you created a separate EBS volume, open **Volumes** and delete the unattached volume.
3. If you created an Elastic IP for this lab, open **Elastic IP addresses** and release it.

You may keep the Lab 2 key pairs and security groups.

Cleanup is complete when both instances are `shutting-down`, `terminated`, or absent from the active instance list, with no extra Lab 2 EBS volume or Elastic IP remaining.
