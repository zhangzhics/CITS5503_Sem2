# Practical Worksheet 8

Version: 1.0 Date: 27/9/2021 Author: Camilo Pestana

Date: 29/07/2024 Updated by Zhi Zhang

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

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

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

## Install ipykernel

It can be installed using pip:

```
pip install ipykernel
```

## Run hyperparameter tuning jobs

We tune hyperparameters from the jupyter notebook we installed above. The instructions for this step are available [here](https://github.com/zhangzhics/CITS5503_Sem2_2023/tree/master/Labs/src/LabAI.ipynb).


Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 










