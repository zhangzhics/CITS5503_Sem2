# Practical Worksheet 8

## Learning Objectives

1. Explore ECR and ECS together with Secrets Manager.
2. Create a dockerfile for the Tuning job.
3. Create a Hyperparameter Tuning Job using Amazon SageMaker.
4. Containerise the Tuning job as a Docker image and push to ECR and deploy as an ECS task.

## Technologies Covered

* AWS
* AWS ECS (Elastic Container Service), Fargate
* AWS ECR (Elastic Container Registry)
* Amazon SageMaker
* boto3
* Python

## Background

The aim of this lab is to create multiple scripts that will:

1. Build a docker image file based on a provided Dockerfile.
2. Push the docker image into ECR and deploy the image via ECS.
3. Access jupyter notebook from the deployed image and implement a Tuning Job via the SageMaker.
4. Use jupyter notebooks and pandas to explore a dataset.
5. Use boto3 and SageMaker to create training and hyperparameter optimization jobs.
6. After a job is learned, SageMaker allows to deploy models in EC2 instances, which is optional.


## Create a Dockerfile and build a Docker image

Create your Dockerfile: (Please read through the Dockerfile carefully, you may need to use the token to access Jupyter Notebook)

```Dockerfile
FROM python:3.10

RUN pip install jupyter boto3 sagemaker awscli
RUN mkdir /notebook

# Use a sample access token
ENV JUPYTER_ENABLE_LAB=yes
ENV JUPYTER_TOKEN="CITS5503"

# Allow access from ALL IPs
RUN jupyter notebook --generate-config
RUN echo "c.NotebookApp.ip = '0.0.0.0'" >> /root/.jupyter/jupyter_notebook_config.py

# Copy the ipynb file
RUN wget -P /notebook https://raw.githubusercontent.com/zhangzhics/CITS5503_Sem2/master/Labs/src/LabAI.ipynb

WORKDIR /notebook
EXPOSE 8888

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]

```

Build your dockerfile:

```bash
docker build -t YOUR_STUDENT_NUMBER-lab8 .
```

After the above script finishes without errors, test your image locally by running:

```bash
docker run -p 8888:8888 YOUR_STUDENT_NUMBER-lab8
```

You can go to 127.0.0.1:8888 to check if a notebook file has been downloaded successfully.

## Prepare ECR via Boto3 scripts on your local machine

### ECR
ECR is a fully managed Docker container registry that allows developers to store, manage, and deploy Docker container images securely and efficiently within AWS.

We use Boto3 script to create a ECR repository:

```python
import boto3

def create_or_check_repository(repository_name):
    ecr_client = boto3.client('ecr')
    try:
        response = ecr_client.describe_repositories(repositoryNames=[repository_name])
        repository_uri = response['repositories'][0]['repositoryUri']
    except ecr_client.exceptions.RepositoryNotFoundException:
        response = ecr_client.create_repository(repositoryName=repository_name)
        repository_uri = response['repository']['repositoryUri']
    return repository_uri


repository_name = 'YOUR_STUDENT_NUMBER' + '_ecr_repo'
repository_uri = create_or_check_repository(repository_name)
print("ECR URI:", repository_uri)
```

This gives you a **ECR URI**, and you need use this uri to push your Dockerfile into the ECR repository.

The following code uses the AWS Boto3 to obtain an authorisation token from AWS ECR, decodes it to retrieve the username and password, and then generates a Docker login command. This allows the user to log into ECR using the produced command, enabling them to push and pull Docker images.

To get the Docker token:

```python
import boto3
import base64
def get_docker_login_cmd():
    ecr_client = boto3.client('ecr')
    token = ecr_client.get_authorization_token()
    username, password = base64.b64decode(token['authorizationData'][0]['authorizationToken']).decode().split(':')
    registry = token['authorizationData'][0]['proxyEndpoint']
    return f"docker login -u {username} -p {password} {registry}"

print(get_docker_login_cmd())
```

You will get the command to grant the Docker access to the ECR repo. You have to run the output command from the script above in your terminal, and you will get "Login Succeeded" from the terminal if it goes well.

**NOTE**: If you're using WSL 2, DNS often breaks and returns an error message such as "no such host". If so, try this:
- Edit your WSL resolv.conf:
  
```bash
sudo rm /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
sudo chattr +i /etc/resolv.conf
```
- Restart WSL2

## Push a local Docker image onto ECR

Once you see the "Login Succeeded" message, you tag and push your local Docker image to your ECR repository via terminal. To tag your image as the latest version, do:

```bash
docker tag YOUR_STUDENT_NUMBER-lab8:latest YOUR_ECR_URI:latest
```

Then push to your ECR:

```bash
docker push YOUR_ECR_URI:latest
```

The step above takes some time to upload, which depends on your internet connection.

## Deploy your Docker image onto ECS
ECS is a fully managed container orchestration service from AWS that allows developers to run, stop, and manage Docker containers on a cluster of virtual machines without needing to install or manage their own container orchestration software.

### Create a task definition for an ECS task:

To inject environment variables into your ECS task, add an environment field in your container definition as follows:

```python
import boto3

def create_ecs_task_definition(
    client, image_uri, account_id, task_role_name, execution_role_name, student_id,
    environment_dict=None, log_group=None, log_region=None, port=8888, cpu='256', memory='512'
):
    task_role_arn = f'arn:aws:iam::{account_id}:role/{task_role_name}'
    execution_role_arn = f'arn:aws:iam::{account_id}:role/{execution_role_name}'

    env_list = [{'name': k, 'value': v} for k, v in (environment_dict or {}).items()]
    
    response = client.register_task_definition(
        family=f'{student_id}-task-family',
        networkMode='awsvpc',
        requiresCompatibilities=['FARGATE'],
        cpu=cpu,
        memory=memory,
        taskRoleArn=task_role_arn,
        executionRoleArn=execution_role_arn,
        containerDefinitions=[
            {
                'name': f'{student_id}-container',
                'image': image_uri,
                'essential': True,
                'portMappings': [
                    {
                        'containerPort': port,
                        'hostPort': port,
                        'protocol': 'tcp'
                    },
                ]
            },
        ],
    )
    return response

account_id = '489389878001'
student_id = "YOUR_STUDENT_NUMBER"
task_role_name = 'SageMakerRole'
execution_role_name = 'ecsTaskExecutionRole'
image_uri = 'YOUR_ECR_URI'


ecs_client = boto3.client('ecs')

task_definition = create_ecs_task_definition(
    ecs_client,
    image_uri,
    account_id,
    task_role_name,
    execution_role_name,
    student_id,
    log_group='/ecs/lab8-service', 
    log_region='ap-southeast-2',   
    port=8888                      
)
print("Task Definition ARN:", task_definition['taskDefinition']['taskDefinitionArn'])

```

The printed task definition ARN is used for the next step.

*NOTE*: For Apple Silicon macOS users, you have built your Docker image on the ARM64 architecture. Thus, you should add a specific runtime platform in your ECS Task Definition to resolve the incompatibility issue as follows:

```python
    response = client.register_task_definition(
        # Other parameters remain the same
        runtimePlatform={           
            'cpuArchitecture': 'ARM64',
            'operatingSystemFamily': 'LINUX'
        }
    )
```

### Create an ECS service:

Fargate is a serverless compute engine for containers that works with ECS, which allows developers to run containers without managing the underlying EC2 instances.

First, create a cluster:

```python
import boto3

def create_ecs_cluster(client, cluster_name):
    response = client.create_cluster(
        clusterName=cluster_name
    )
    return response
```

Then, create an ECS service:

```python
def create_ecs_service(client, cluster_name, service_name, task_definition, subnet_ids, security_group_ids):
    response = client.create_service(
        cluster=cluster_name,
        serviceName=service_name,
        taskDefinition=task_definition,
        desiredCount=1,
        launchType='FARGATE',
        networkConfiguration={
            'awsvpcConfiguration': {
                'subnets': subnet_ids,
                'securityGroups': security_group_ids,
                'assignPublicIp': 'ENABLED'
            }
        },
        deploymentConfiguration={
            'maximumPercent': 200,
            'minimumHealthyPercent': 100
        }
    )
    return response
```

To invoke the functions above, you first need to configure a security group from AWS console that allows **outbound HTTPS (port 443)** and **inbound TCP (port 8888)**. You then need the following code:

*NOTE*: remember to update the values in relevant variables below. For variables of **subnet_id_1**, **subnet_id_2**, and **subnet_id_3**, retrieve their values from AWS console based on your region.

```python
#This function is to check when the service becomes stable
def wait_for_service_stability(client, cluster_name, service_name):
    waiter = client.get_waiter('services_stable')
    waiter.wait(cluster=cluster_name, services=[service_name])

ecs_client = boto3.client('ecs')

student_id = "YOUR_STUDENT_NUMBER"
ECR_image_uri = 'YOUR_ECR_URI'

cluster_name = student_id + '-cluster'
create_ecs_cluster(ecs_client, cluster_name)

service_name = student_id + '-service'
task_definition = 'YOUR_TASK_DEFINITION_ARN'
subnet_id_1= 'YOUR_SUBNET_ID_1'
subnet_id_2= 'YOUR_SUBNET_ID_2'
subnet_id_3= 'YOUR_SUBNET_ID_3'

subnet_ids = [subnet_id_1, subnet_id_2, subnet_id_3]
security_group_ids = ['YOUR_SECURITY_GROUP_ID']

ecs_client = boto3.client('ecs')

service_response = create_ecs_service(ecs_client, cluster_name, service_name, task_definition, subnet_ids, security_group_ids)
print(f'ECS Service created: {service_response["service"]["serviceArn"]}')

print(f'Waiting for service {service_name} to become stable...')
wait_for_service_stability(ecs_client, cluster_name, service_name)
print(f'Service {service_name} is now stable.')
```

### Get a public IP address

Remember to update relevant variables below:

```bash
aws ecs describe-tasks \
    --cluster YOUR_CLUSTER_NAME \
    --tasks $(aws ecs list-tasks --cluster YOUR_CLUSTER_NAME --service-name YOUR_SERVICE_NAME --query 'taskArns[0]' --output text) \
    --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' \
    --output text | xargs -I {} aws ec2 describe-network-interfaces \
    --network-interface-ids {} \
    --query 'NetworkInterfaces[0].Association.PublicIp' \
    --output text
```

## Run Hyperparameter Tuning Jobs

For this step, it is detailed in the notebook [here](https://github.com/zhangzhics/CITS5503_Lab_89/blob/fa717b69fde7845ff8f3278cdd7a71caf18aaf99/lab8/LabAI.ipynb). 

**NOTE**: The notebook has been copied to your Docker image in the first step. It needs to be updated before you run it. 

Once the notebook has been updated, open a browser and navigate to the following address to run it within your ECS. Your public IP address was returned in the previous step.

```
<YOUR PUBLIC IP>:8888
```

**NOTE**: Delete relevant ECR, ECS and S3 resources from AWS console after the lab is done.

## Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). Terminate your ECS task and delete your ECR repository.
