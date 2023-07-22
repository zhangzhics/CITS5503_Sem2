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

Note: please use your Ubuntu VM – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:

1. Use jupyter notebooks and pandas to explore a dataset.
2. Use boto3 and sagemaker to create training and hyperparameter optimization jobs, two important steps in every machine learning project.
3. After a job is learned SageMaker allows to deploy models in EC2 instances. However, this is out of the scope for this lab.

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

The steps for this lab are stored in the notebook [here](https://github.com/uwacsp/cits5503/blob/master/Labs/src/LabAI.ipynb)
If you have installed the required packages as above you can skip the first installation step in the notebook.

Remember to add your student ID to the session preparation cell as well as creating a bucket for the data to be loaded in to. This bucket cannot be restricted to only accessible for yourself as per a previous lab.

The notebook contains all of the instructions required. Please do not edit any of the machine learning code as it has been specifically designed to work on the resources available whilst minimising costs. You should only have to edit resource names to complete this lab.

The tuning will normally take between 2-4 minutes, review the output and the best hyperparameters to ensure that the job was completed successfully.


Lab Assessment:

A structured description (15%). A clear step-by-step with detailed descriptions (85%). 










