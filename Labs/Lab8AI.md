# Practical Worksheet 8

Version: 1.0 Date: 27/9/2021 Author: Camilo Pestana

Date: 21/07/2023 Updated by Zhi Zhang

## Learning Objectives

1. Download data to be explored and then upload to S3
2. Create a Hyperparameter Tuning Job using Amazon SageMaker

## Technologies Covered

* Ubuntu
* AWS
* Amazon SageMaker
* S3
* boto3
* Python

Note: please use your Linux VM – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:

1. Use jupyter notebooks and pandas to explore a dataset.
2. Use boto3 and sagemaker to create training and hyperparameter optimization jobs, two important steps in every machine learning project.
3. After a job is learned SageMaker allows to deploy models in EC2 instances. However, this is out of the scope for this lab.
## Install and run jupyter notebooks
```
pip install notebook
jupyter notebook
```

## Set Up Python Environment

This lab is best done using a Python virtual environment for your packages, or within your own VM. If you are using a virtual environment, remember to activate it prior to installing packages.

To run the SageMaker commands contained within the notebook the following packages are required:
- sagemaker
- pandas
- ipykernel

This can be installed using pip:
```
pip install sagemaker pandas ipykernel
```
or
```
pip3 install sagemaker pandas ipykernel
```
depending on your Python installation.

## Run Hyperparameter Tuning Jobs

The steps for this lab are stored in the notebook [here](https://github.com/zhangzhics/CITS5503_Sem2_2023/tree/master/Labs/src/LabAI.ipynb).

* Note: If you meet this error, ClientError: Non-numeric value 'F' found in the header line 'False,54,3,999,0,1,0,False,False,False,False,False...' of file 'train.csv', you can use Python to convert True/False to 1/0.

If you have installed the required packages as above you can skip the first installation step in the notebook. Then the following packegaes are required 
```
import pandas as pd
import numpy as np
import boto3
import os
```

Remember to add your student ID to the session preparation cell as well as creating a bucket for the data to be loaded into. This bucket cannot be restricted to only accessible for yourself as per a previous lab.

After you apload training and validation files to your bucket, you can follow [Configure and Launch a Hyperparameter Tuning Job](https://docs.aws.amazon.com/en_kr/sagemaker/latest/dg/automatic-model-tuning-ex-tuning-job.html), to create Hyperparameter tuning jobs. 

The notebook contains all of the instructions required. Please do not edit any of the machine learning code as it has been specifically designed to work on the resources available whilst minimizing costs. You should only edit resource names to complete this lab.

The tuning will normally take between 2-4 minutes, review the output and the best hyperparameters to ensure that the job is completed successfully.


Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 










