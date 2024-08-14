# Practical Worksheet 3

Version: 1.0 Date: 12/04/2018 Author: David Glance

Date: 24/07/2024 Updated by Zhi Zhang

## Learning Objectives

1. Learn how to create and configure S3 buckets and read and write objects to them
2. Learn how to use operations on DynamoDB: Create table, put items, get items
3. Start an application as your own personal Cloud Storage

## Technologies Covered

* Ubuntu
* AWS
* AWS S3
* AWS DynamoDB
* Python/Boto scripts
* VirtualBox

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a program that will:

[1] Scan a directory and upload all of the files found in a directory to an S3 bucket, preserving the path information

[2] Store information about each file uploaded to S3 in a DynamoDB

[3] Restore the directory on a local drive using the files in S3 and the information in DynamoDB

## Program

### [1] Preparation

Download the python code `cloudstorage.py` from the directory of [src](https://github.com/zhangzhics/CITS5503_Sem2/blob/master/Labs/src/cloudstorage.py) \
Create a directory `rootdir` \
Create a file in `rootdir` called `rootfile.txt` and write some content in it `1\n2\n3\n4\n5\n` \
Create a second directory in rootdir called `subdir`, and in the `subdir` directory create another file `subfile.txt` with the same content as `rootfile.txt`.

### [2] Save to S3 by updating `cloudstorage.py`

Modify the downloaded Python script, `cloudstorage.py`, to create an S3 bucket named `<student ID>-cloudstorage`.

When the program traverses the directory starting at the root directory `rootdir`, upload each file onto the S3 bucket. An easy way to upload files is to use the command below:

```
s3.upload_file()
```

**NOTE**: Make sure your S3 bucket has the same file structure as shown in `[1] Preparation`.

### [3] Restore from S3

Create a new program called `restorefromcloud.py` that reads the S3 bucket and writes the contents of the bucket within the appropriate directories. 

**NOTE**: Your local Linux environment should see a copy of the files and the directories from the S3 bucket.

### [4] Write information about files to DynamoDB

Install DynamoDB on your Linux environment

```
mkdir dynamodb
cd dynamodb
```

Install jre if not done

```
sudo apt-get install default-jre
wget https://s3-ap-northeast-1.amazonaws.com/dynamodb-local-tokyo/dynamodb_local_latest.tar.gz
```

You can use the following command to extract files from dynamodb_local_latest.tar.gz

```
tar -zxvf dynamodb_local_latest.tar.gz
```

After the extraction, run the command below

```
java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar –sharedDb
```

Alternatively, you can use docker:
```
docker run -p 8000:8000 amazon/dynamodb-local -jar DynamoDBLocal.jar -inMemory -sharedDb
```
**Note**: Do not close the current window, open a new window to run the following Python script.

Write a Python script to create a table called `CloudFiles` on your local DynamoDB and the attributes for the table are:

```
        CloudFiles = {
            'userId',
            'fileName',
            'path',
            'lastUpdated',
	    'owner',
            'permissions'
            }
        )
```
`userId` is the partition key and `fileName` is the sort key. Regarding the creation, refer to this [page](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html)

Then, you need to get the attributes above for each file of the S3 bucket and then write the attributes of each file into the created DynamoDB table. Regarding how to get the attributes for a file, refer to this [page](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/get_object_acl.html)

**NOTE**: 

1) The table should have 2 items. One item corresponds to one file in the bucket and consists of the attributes above and their values.

2) Regarding the attribute `owner`, if you use a region in the table below, its value should be **owner's name**. Otherwise, its value should be **owner's ID**.

| Region | Region Name |
| --- | --- |
| US East (N. Virginia) | us-east-1 |
| Asia Pacific (Tokyo)	| ap-northeast-1 |
| Asia Pacific (Singapore) | ap-southeast-1 |
| Asia Pacific (Sydney)	| ap-southeast-2 |


### [5] Scan the table

Use AWS CLI command to scan the created DynamoDB table, and output what you've got. 

### [6] Delete the table

Use AWS CLI command to delete the table.

**NOTE**: Delete the created S3 bucket from AWS console after the lab is done.

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 
