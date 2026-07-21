# Lab 4 - KMS & Encryption

## Learning Objectives

1. IAM policies applied to S3
2. KMS Key Management System – creating keys and using the key for symmetric encryption
3. Using AES Encryption for client- and server-side encryption

## Technologies Covered

* Ubuntu
* AWS
* AWS KMS
* AES Encryption
* Python/Boto scripts
* VirtualBox

## Background

The aim of this lab is to write a program that will:

1. Apply a policy to your bucket to allow only you as a user to access it
2. Create a key in KMS and use it to encrypt files on the client before uploading to S3 and decrypt them after downloading from S3
3. Implement AES using Python and test the difference in performance between the KMS solution and the local one.

## Apply a policy to restrict permissions on the bucket

### \[1] Write a Python script

Apply the following policy to an S3 bucket to allow only your username to access the bucket. Make appropriate changes (e.g., `Resource`, `Condition`, etc) to the policy as necessary.

**NOTE**:

1. In the policy below, **you should replace `<your_s3_bucket>` with the S3 bucket you created as `<student ID>-cloudstorage` and `<studentnumber>` with your own student number**. You can use the AWS console to create the S3 bucket in this lab that has the same contents as the bucket in the last lab.
2. Check if your `Resource` section includes permissions for both the bucket itself AND the objects inside it.

```
{
  "Version": "2012-10-17",
  "Statement": [{
   "Sid": "AllowAllS3ActionsInUserFolderForUserOnly",
    "Effect": "DENY",
    "Principal": "*",
    "Action": "s3:*",
    "Resource": "arn:aws:s3:::<your_s3_bucket>/folder1/folder2/*",
    "Condition": {
      "StringNotLike": {
          "aws:username":"<studentnumber>@student.uwa.edu.au"
       }
    }
  }]
}
```

### \[2] Check whether the script works

Use the AWS CLI command and the AWS S3 console to display the policy content applied to the S3 bucket.

Test the policy by using a username that is not your own to access the folder called `rootdir` and output what you've got.

## AES Encryption using KMS

### \[1] Create a KMS key

Write a Python script to create a KMS key, where your student number works as an alias for the key.

### \[2] Attach a policy to the created KMS key

Update the script to attach the following policy to the key.

**NOTE**: in the policy below, **you should replace all `<your_username>` with your own username**.

```
{
  "Version": "2012-10-17",
  "Id": "key-consolepolicy-3",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::489389878001:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::489389878001:user/<your_username>"
      },
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Get*",
        "kms:Delete*",
        "kms:TagResource",
        "kms:UntagResource",
        "kms:ScheduleKeyDeletion",
        "kms:CancelKeyDeletion"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::489389878001:user/<your_username>"
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::489389878001:user/<your_username>"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    }
  ]
}
```

### \[3] Check whether the script works

Use the AWS KMS console to test whether your username is the key administrator and key user.

**NOTE**: After you log in to the console, you perform the test by showing the policy you created, i.e., which ARN is the key administrator and which ARN is the key user.

### \[4] Use the created KMS key for encryption/decryption

Write a Python script that encrypts each file in the S3 bucket and then decrypts it using the created KMS key. Both encrypted and decrypted files will be in the same folder as the original file.

### \[5] Apply `pycryptodome` for encryption/decryption

Write another Python script that uses the Python library `pycryptodome` to encrypt and decrypt each file in the S3 bucket. Both encrypted and decrypted files will be in the same folder as the original file.

For encryption/decryption, refer to the example code below.

{% file src="../.gitbook/assets/fileencrypt.py" %}

### \[6] Uploading

Upload all encrypted and decrypted files to your S3 bucket.

**NOTE**: Delete the created S3 bucket(s) and KMS key(s) from the AWS console after the lab is done.

## Live Lab Assessment (2 marks)

Make sure you have practiced and completed all the required checkpoints before joining the marking queue. When you are next in line, have your commands/scripts/cleanup ready so that you can demonstrate each checkpoint live to a lab facilitator for verification. 

You have only ONE attempt to demonstrate each checkpoint in front of the lab facilitator. If you fail any checkpoint, the corresponding marks will not be awarded. Screenshots, copied output, and prerecorded demonstrations are not accepted.

### Checkpoint 1: Bucket policy (0.5 mark)

First, run the following command successfully where `<your_s3_bucket>` is an S3 bucket you created:

```bash
aws s3api get-bucket-policy \
  --bucket <your_s3_bucket> \
  --query Policy \
  --output text
```

**NOTE**: Make sure the returned policy shows that only you can access the bucket and that all other users are denied access.

Second, from the AWS S3 console, use your AWS account to successfully access a folder called `rootdir` inside the S3 bucket.

Last, from the AWS S3 console, a facilitator tries to access `rootdir` using their AWS account and sees `AccessDenied`.

### Checkpoint 2: KMS configuration (0.5 mark)

First, open AWS Management Console → KMS → Customer managed keys and select the key whose alias name is your student number. 

Second, also from the console, open the key policy attached the selected key. The key policy lists your IAM ARN as both a key administrator and key user.

### Checkpoint 3: pycryptodome (0.5 mark)

First, open your updated `fileencrypt.py`, and show the part that encrypts and decrypts each file in your S3 bucket.

Second, run the script live until it completes.

Last, navigate to your AWS Management Console → S3 → Buckets and show the original, encrypted, and decrypted objects produced by the script.

### Checkpoint 4: Cleanup (0.5 mark)

After the first two checkpoints are completed, you must open AWS Management Console → S3 → Buckets, select the ones you created for deletion.

**NOTE**: the bucket must be empty before deletion starts. DynamoDB Local runs on your laptop and is outside the cleanup check.
