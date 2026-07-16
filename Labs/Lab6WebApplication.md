# Lab 6 - Web Application

## Learning Objectives

1. Create a web app using Django
2. Implement nginx and load balance requests to it
3. Retrieve data from DynamoDB to display in the app

## Technologies Covered

* Ubuntu
* AWS
* AWS ELB
* RDS
* Python/Boto scripts

## Background

The aim of this lab is to write a program that will:

\[1] Understand the basis for a web architecture that incorporates scalability and security using ELB

\[2] Familiarise yourself with the basics of programming using Django

## Set up an EC2 instance

### \[1] Create an EC2 micro instance with Ubuntu and SSH into it

**NOTE**: Regarding your region, find it in the [region table in Lab 1](Lab1IntroSetup.md#id-3-configure-aws).

### \[2] Install the Python 3 virtual environment package

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python3-venv
```

It is easier now if you change the bash to operate as sudo

```
sudo bash
```

### \[3] Access a directory

Create a directory with a path `/opt/wwc/mysites` and `cd` into the directory.

### \[4] Set up a virtual environment

```
python3 -m venv myvenv
```

### \[5] Activate the virtual environment

```
source myvenv/bin/activate

pip install django

django-admin startproject lab

cd lab

python3 manage.py startapp polls
```

**NOTE**: Stop and look at the files that have been created – the project files are to do with the running of the application. We will deal with the files as we go through.

### \[6] Install nginx

```
apt install nginx
```

### \[7] Configure nginx

edit `/etc/nginx/sites-enabled/default` and replace the contents of the file with

```
server {
  listen 80 default_server;
  listen [::]:80 default_server;

  location / {
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Real-IP $remote_addr;

    proxy_pass http://127.0.0.1:8000;
  }
}
```

### \[8] Restart nginx

```
service nginx restart
```

### \[9] Access your EC2 instance

In your app directory: `/opt/wwc/mysites/lab`, run:

```
python3 manage.py runserver 8000
```

Open a browser and enter the IP address of your EC2 instance. Output what you've got and stop your server with CONTROL-C

## Set up Django inside the created EC2 instance

### \[1] Edit the following files (create them if not exist)

edit polls/views.py

```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world.")
```

edit polls/urls.py

```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

edit lab/urls.py

```
from django.urls import include, path
from django.contrib import admin

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

### \[2] Run the web server again

```
python3 manage.py runserver 8000
```

### \[3] Access the EC2 instance

Access the URL: `http://<ip address of your EC2 instance>/polls/`, and output what you've got.

**NOTE**: remember to put the `/polls/` on the end, and you may need to restart `nginx` if it does not work.

## Set up an ALB

### \[1] Create an application load balancer

* Specify the region and subnet where your EC2 instance resides.
* Create a listener with a default rule: Protocol: `HTTP` and Port `80` forwarding.
* Choose the security group, allowing `HTTP` traffic.
* Add your instance as a registered target.

### \[2] Health check

For the target group, specify `/polls/`as the path for the health check.

Confirm the health check fetch the `/polls/` page every 30 seconds.

### \[3] Access

Access the URL: `http://<load balancer dns name>/polls/`, and output what you've got.

## Web interface for CloudStorage application

You need to create an AWS DynamoDB table containing the CloudStorage file data used in Lab 3. If you did not retain that data, repeat the relevant Lab 3 preparation steps to recreate it. Configure boto3 through your AWS CLI profile or an approved IAM role; do not copy credentials into the source code. You can run your Django application locally.

In `views.py`, add `boto3` code to scan the AWS DynamoDB table. Display the results in the calling page.

In Django, you can use a template to properly format a web page using supplied variables – you can do that to make the table look nice. To use a template, you need to create a folder called templates under polls and add it to the TEMPLATES section of `lab/settings.py` .

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            'polls/templates/'
        ],
```

In the templates directory, add a file `files.html` with the following contents:

```
<html>
<head>
    <title>Files</title>
</head>
<body>
    <h1>Files </h1>


    <ul>
        {% for item in items %}
          <li>{{ item.fileName }}</li>
	{% endfor %}
    </ul>

</body>
</html>
```

Finally, in `views.py`, you can pass variables from your DynamoDB call and render the template in the following way:

```
from django.shortcuts import render
from django.template import loader
from django.http import HttpResponse
import boto3
import json
from boto3.dynamodb.conditions import Key, Attr
from botocore.exceptions import ClientError

def index(request):
    template = loader.get_template('files.html')

    dynamodb = boto3.resource('dynamodb', region_name='<Your assigned region>')

    table = dynamodb.Table("UserFiles")

    items = []
    try:
        response = table.scan()

    except ClientError as e:
        print(e.response['Error']['Message'])
    else:    
        context = {'items': response['Items'] }

        return HttpResponse(template.render(context, request))
```

You can add variables to the template and more formatting functionality to display the information correctly.

**NOTE**: After marking, complete the chargeable-resource cleanup described below.

## Live Assessment Checkpoints

You do not need to submit a written report for this lab. Complete the EC2, Django, nginx, ALB, and DynamoDB work before joining the marking queue. Keep the AWS resources running until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Have the cleanup Console pages or commands ready before joining the queue. Screenshots and saved output do not replace live results.

Open the EC2, Load Balancer, and DynamoDB Console pages and the direct and load-balanced `/polls/` pages before joining the queue. Do not display or hard-code AWS credentials.

### Checkpoint 1: Student-owned EC2, Django, and nginx — 0.5 marks

Open AWS Console → EC2 → Instances, refresh the page, and select your Lab 6 instance. Show its instance ID, student-number Name tag, `Running` state, assigned region and availability zone, and public IP address.

Refresh:

```text
http://<public-ip>/polls/
```

The page must load through nginx, and its IP must match the selected EC2 instance.

CLI alternative if the Console does not display the instance details promptly:

```bash
aws ec2 describe-instances \
  --instance-ids <lab6-instance-id> \
  --query 'Reservations[].Instances[].{InstanceId:InstanceId,Name:Tags[?Key==`Name`]|[0].Value,AZ:Placement.AvailabilityZone,State:State.Name,PublicIp:PublicIpAddress}' \
  --output table
```

### Checkpoint 2: Application Load Balancer — 0.5 marks

Open AWS Console → EC2 → Load Balancers, refresh the page, and select your Lab 6 ALB. Follow its listener and target-group links. Show:

- The Lab 6 instance registered as a target.
- Health check path `/polls/`.
- Target status `healthy`.
- The HTTP listener forwarding to that target group.

Refresh:

```text
http://<load-balancer-dns-name>/polls/
```

The Django page must load through the ALB DNS name.

CLI alternatives if the Console does not display the listener or target-health details promptly:

```bash
aws elbv2 describe-target-groups \
  --names <lab6-target-group-name> \
  --query 'TargetGroups[0].{Arn:TargetGroupArn,Path:HealthCheckPath,Port:Port,Protocol:Protocol}' \
  --output table

aws elbv2 describe-target-health \
  --target-group-arn <lab6-target-group-arn> \
  --query 'TargetHealthDescriptions[].{Instance:Target.Id,Health:TargetHealth.State}' \
  --output table

aws elbv2 describe-listeners \
  --load-balancer-arn <lab6-load-balancer-arn> \
  --query 'Listeners[].{Port:Port,Protocol:Protocol,ForwardTo:DefaultActions[0].TargetGroupArn}' \
  --output table
```

### Checkpoint 3: DynamoDB integration — 1 mark

Open AWS Console → DynamoDB → Tables, select your Lab 6 table, and open **Explore table items**. Refresh the table and show the CloudStorage file records and attributes produced by your script. Unrelated sample rows do not meet this checkpoint.

CLI alternative:

```bash
aws dynamodb scan \
  --table-name <lab6-cloudstorage-table> \
  --output table
```

Refresh the Django page. The filenames on the page must match the refreshed DynamoDB items. Be ready to show the `table.scan()` call and the template loop that renders `fileName` if the source of the page data is unclear.

You may recreate this data during Lab 6. The original Lab 3 S3 bucket and local DynamoDB table do not need to exist.

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes all three checkpoints. Use the AWS Console or commands/script in this order:

1. Delete the Application Load Balancer.
2. Terminate the EC2 instance; `Stopped` is not a completed cleanup state.
3. Delete the Lab 6 AWS DynamoDB table.
4. If you created a separate EBS volume or Elastic IP for this lab, delete the unattached volume or release the address.

You may keep the target group, key pair, and security group. Cleanup is complete when the deletion requests are accepted and no Lab 6 compute, load-balancing, or DynamoDB resource remains active.

CLI alternatives:

```bash
aws elbv2 delete-load-balancer \
  --load-balancer-arn <load-balancer-arn>

aws ec2 terminate-instances \
  --instance-ids <lab6-instance-id>

aws dynamodb delete-table \
  --table-name <lab6-cloudstorage-table>
```
