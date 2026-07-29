# Lab 6 - Web Application

## Learning Objectives

1. Create a web app using Django
2. Implement nginx and load balance requests to it
3. Retrieve data from DynamoDB to display in the app

## Technologies Covered

* AWS
* AWS ELB
* RDS
* Python/Boto scripts

## Background

The aim of this lab is to write a program that will:

\[1] Understand the basis for a web architecture that incorporates scalability and security using ELB

\[2] Familiarise yourself with the basics of programming using Django

## Set up an EC2 instance

### \[1] Create an EC2 instance and SSH into it

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

Access the URL: `http://<ip address of your EC2 instance>/polls/`, and see what you've got.

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

Access the URL: `http://<load balancer dns name>/polls/`, and see what you've got.

## Web interface for CloudStorage application

You need to create an AWS DynamoDB table by copying it from the local DynamoDB used in Lab 3, as well as provide a copy of your AWS credentials. If you did not save the data from Lab 3, you will need to repeat some steps to retrieve the files from the S3 bucket and use them as attributes to be stored in the AWS DynamoDB table. 

In `views.py`, add `boto3` code to scan the AWS DynamoDB table. Display the results in the calling page.

In your Django application, you can use a template to properly format a web page using supplied variables – you can do that to make the table look nice. To use a template, you need to create a folder called templates under polls and add it to the TEMPLATES section of `lab/settings.py` .

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

    dynamodb = boto3.resource('dynamodb', region_name='<Your assigned region>',
                              aws_access_key_id='Your Access Key',
                              aws_secret_access_key='Your Secret')

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

**NOTE**: Delete all the created AWS resources from the AWS Management console after the lab is done.

## Live Lab Assessment (2 marks)

Make sure you have practiced and completed all the required checkpoints before joining the marking queue. When you are next in line, have your commands/scripts/cleanup ready so that you can demonstrate each checkpoint live to a lab facilitator for verification. 

You have only ONE attempt to demonstrate each checkpoint in front of the lab facilitator. If you fail any checkpoint, the corresponding marks will not be awarded. Screenshots, copied output, and prerecorded demonstrations are not accepted.

### Checkpoint 1: EC2, Django, and nginx (0.5 mark)

First, from your AWS Management Console, show:

- A running EC2 instance follows the naming format of `<student-number>-vm1`.
- The instance is t3.micro.
- The instance resides in the assigned region, according to the [region table in Lab 1](Lab1IntroSetup.md#id-3-configure-aws).
- The instance has a public IP address.

Second, from your local browser tab, enter `http://<your-instance-public-ip>/polls/`. The web access must be successful. 

### Checkpoint 2: Application Load Balancer (0.5 mark)

First, open your script used to set up an ALB, and show the part that creates an ALB.

Second, open your AWS Management Console, and show:

- The instance is registered as a target.
- The Health check path is set to `/polls/` with Interval set to `30` seconds.
- The registered target status is `Healthy`.

### Checkpoint 3: AWS DynamoDB (0.5 mark)

First, open your script about web interface for CloudStorage application, and show the part that creates an AWS DynamoDB table.

Second, from your terminal, run the command below while your DynamoDB Local is running:

```bash
aws dynamodb scan \
  --table-name <your_local_DynamoDB_table> \
  --endpoint-url http://localhost:8000 \
  --output table
```

Last, from your local browser tab, enter `http://<your-load-balancer-dns-name>/polls/` and show the list of file names. These names are contained in the scan output from your second step.

### Checkpoint 4: Cleanup (0.5 mark)

After the first three checkpoints are completed, open AWS Management Console and complete the following actions in order:

1. Delete application load balancers you created.
2. Delete/Terminate instances you created.
3. Delete AWS DynamoDB Tables you created.
4. Release any Elastic IP addresses that are allocated.
