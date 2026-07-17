# Lab 8 - Container & AI

## Learning Objectives

1. Explore ECR and ECS together with Secrets Manager.
2. Create a dockerfile for the Tuning job.
3. Create a Hyperparameter Tuning Job using Amazon SageMaker.
4. Containerise the Tuning job as a Docker image and push to ECR and deploy as an ECS task.

## Technologies Covered

* AWS ECS (Elastic Container Service), Fargate
* AWS ECR (Elastic Container Registry)
* Amazon SageMaker
* boto3
* Python

## Background

The aim of this lab is to create multiple scripts that will:

1. Build a Docker image file based on a provided Dockerfile.
2. Push the Docker image into ECR and deploy the image via ECS.
3. Access a Jupyter notebook from the deployed image and implement a Tuning Job via SageMaker.
4. Use Jupyter notebooks and pandas to explore a dataset.
5. Use boto3 and SageMaker to create training and hyperparameter optimisation jobs.

## Create a Dockerfile and build a Docker image

Create your Dockerfile: (Please read through the Dockerfile carefully; you may need to use the token to access Jupyter Notebook)

```dockerfile
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

Build your Dockerfile:

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

We use a boto3 script to create an ECR repository:

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

This gives you an **ECR URI**, and you need use this uri to push your Dockerfile into the ECR repository.

The following code uses AWS Boto3 to obtain an authorisation token from AWS ECR, decodes it to retrieve the username and password, and then generates a Docker login command. This allows the user to log into ECR using the produced command, enabling them to push and pull Docker images.

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

You will get the command to grant Docker access to the ECR repo. You have to run the output command from the script above in your terminal, and you will get "Login Succeeded" from the terminal if it goes well.

**NOTE**: If you're using WSL2, DNS often breaks and returns an error message such as "no such host". If so, try this:

* Edit your WSL resolv.conf:

```bash
sudo rm /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
sudo chattr +i /etc/resolv.conf
```

* Restart WSL2

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
    environment_dict=None,port=8888, cpu='256', memory='512'
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
    port=8888                      
)
print("Task Definition ARN:", task_definition['taskDefinition']['taskDefinitionArn'])

```

The printed task definition ARN is used for the next step.

_NOTE_: For Apple Silicon macOS users, you have built your Docker image on the ARM64 architecture. Thus, you should add a specific runtime platform in your ECS Task Definition to resolve the incompatibility issue as follows:

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

To invoke the functions above, you first need to configure a security group from the AWS console that allows **outbound HTTPS (port 443)** and **inbound TCP (port 8888)**. You then need the following code:

_NOTE_: Remember to update the values in the relevant variables below. For variables of **subnet\_id\_1**, **subnet\_id\_2**, and **subnet\_id\_3**, retrieve their values from the AWS console based on your region.

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

For this step, it is detailed in the notebook here.

{% file src="../.gitbook/assets/LabAI.ipynb" %}

Open a browser and navigate to the following address to run it within your ECS. Your public IP address was returned in the previous step.

```
<YOUR PUBLIC IP>:8888
```

**NOTE**: Delete relevant ECR, ECS and S3 resources from the AWS console after the lab is done.

## Live Assessment Checkpoints

Attend your scheduled lab and ask a lab facilitator to check your checkpoints in person. Complete the image build and push, ECS deployment, and SageMaker tuning job before joining the marking queue. Keep the AWS resources running until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Open every Console page listed below and prepare cleanup before joining the queue. Screenshots and saved output do not replace live results.

The facilitator will refresh existing results. You will not rebuild the image, redeploy ECS, or rerun the tuning job during marking.

Before joining the queue:

- Start the local Docker container and open Jupyter at `http://127.0.0.1:8888`.
- Wait until the ECS service is stable and its task is running.
- Wait until the SageMaker hyperparameter tuning job is complete.
- Open the ECR **Images** list, the ECS service and running task, the completed SageMaker tuning job, and the exact S3 training prefix in separate tabs. During marking, refresh these tabs instead of navigating from each service's home page.
- Open each resource's cleanup control in the AWS Console before joining the queue. Do not run cleanup until the facilitator finishes all three checkpoints.

### Checkpoint 1: Local Docker image and ECR — 1 mark

Run:

```bash
docker images
docker ps
```

Show a running local container whose image tag contains your student number. Refresh `http://127.0.0.1:8888` and show `LabAI.ipynb` in Jupyter.

Then open AWS Console → Elastic Container Registry → Repositories, select your repository, and refresh the **Images** list. Match the demonstrated local image to an ECR image tag or digest.

CLI alternative if the Console does not display the image details promptly:

```bash
aws ecr describe-images \
  --repository-name <student-repository> \
  --output table
```

### Checkpoint 2: ECS deployment — 1 mark

Open AWS Console → Elastic Container Service → Clusters, select your cluster, and refresh the **Services** and **Tasks** tabs. Open the Lab 8 service and its running task. Show that:

- The service is active.
- Running count equals desired count.
- The running task uses Fargate.
- The task definition's image URI matches the ECR image from Checkpoint 1.
- The task's networking details contain a public IP.

CLI alternative for the service summary:

```bash
aws ecs describe-services \
  --cluster <student-cluster> \
  --services <student-service> \
  --query 'services[0].{Status:status,Running:runningCount,Desired:desiredCount,TaskDefinition:taskDefinition}' \
  --output table
```

Refresh the remote Jupyter page:

```text
http://<task-public-ip>:8888
```

The remote page must contain `LabAI.ipynb` and use the image demonstrated in Checkpoint 1.

### Checkpoint 3: SageMaker hyperparameter tuning — 1 mark

Open AWS Console → SageMaker AI → Training → Hyperparameter tuning jobs, refresh the list, and select your Lab 8 job. Show that:

- The tuning status is `Completed`.
- A best training job is identified.
- The final objective metric and value are present.
- The best training job's model artefact points to your S3 location.

Follow the model artefact S3 location in the Console. Show these objects under your `sagemaker/<student-number>-hpo-xgboost-dm/` prefix:

- `train/train.csv`
- `validation/validation.csv`
- The best training job's model artefact ending in `output/model.tar.gz`

CLI alternatives if the Console does not display the job details promptly:

```bash
aws sagemaker describe-hyper-parameter-tuning-job \
  --hyper-parameter-tuning-job-name <job-name> \
  --query '{Status:HyperParameterTuningJobStatus,BestJob:BestTrainingJob.TrainingJobName,Metric:BestTrainingJob.FinalHyperParameterTuningJobObjectiveMetric}' \
  --output table

aws sagemaker describe-training-job \
  --training-job-name <best-training-job-name> \
  --query 'ModelArtifacts.S3ModelArtifacts' \
  --output text
```

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes all three checkpoints. Use the already-open AWS Console controls in this order:

1. Open AWS Console → ECS → Clusters → your cluster → Services, update the service, and set **Desired tasks** to `0`. Confirm that AWS accepts the update. The Fargate task may stop asynchronously.
2. Open AWS Console → ECR → Repositories → your repository, select every image you created, and choose **Delete**.
3. Open AWS Console → S3, then delete the Lab 8 training input and output prefixes. If you created the whole bucket for Lab 8, empty and delete it.
Completed SageMaker tuning and training job records remain visible and do not count as active resources. Keep shared unit IAM roles such as `SageMakerRole` and `ecsTaskExecutionRole`. You may also keep the inactive ECS service, cluster, task definitions, empty ECR repository, and security group.

The ECS scale-down may finish asynchronously. If AWS accepts `desired count = 0` and the task is stopping, the facilitator can record cleanup as pending; you do not need to wait at the marking station. Cleanup is complete when the scale-down is accepted, the ECR repository contains none of your images, and your Lab 8 S3 data is absent.
