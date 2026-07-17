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

1. In the policy below, **you should replace `<your_s3_bucket>` with the S3 bucket you created and `<studentnumber>` with your own student number**. You can use the AWS console to create the S3 bucket in this lab that has the same contents as the bucket in the last lab.
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

## Live Assessment Checkpoints

Attend your scheduled lab and ask a lab facilitator to check your checkpoints in person. Complete the bucket policy, KMS setup, and both encryption scripts before joining the marking queue. Keep the Lab 4 bucket and KMS key until the facilitator completes the checkpoints. The checkpoints and cleanup take no more than four minutes. Open the S3 and KMS Console cleanup pages before joining the queue. Screenshots and saved output do not replace live results.

You may create a new Lab 4 bucket with the same contents as Lab 3. You do not need to retain the Lab 3 bucket.

### Checkpoint 1: Bucket permission — 0.5 marks

Prepare your S3 bucket policy and keep the protected `rootdir` objects in the bucket. Show the policy in the S3 Console or run:

```bash
aws s3api get-bucket-policy \
  --bucket <student-bucket> \
  --query Policy \
  --output text
```

The policy must use your bucket name and IAM username, cover the bucket ARN and the object ARN ending in `/*` or the intended prefix, deny another user, and preserve your own access.

The facilitator will test access with a facilitator-controlled administrator account. The administrator test must return `AccessDenied`, while your identity can still list the protected objects. The facilitator will not enter administrator credentials on your laptop.

### Checkpoint 2: KMS configuration — 0.5 marks

Open AWS Console → KMS → Customer managed keys and select the key whose alias contains your student number. Be ready to show that:

- The key is enabled and belongs to your account.
- The alias contains your student number.
- The key policy lists your IAM ARN as both a key administrator and key user.

CLI alternative if the Console does not display the details promptly:

```bash
aws kms list-aliases --output table
aws kms describe-key \
  --key-id alias/<student-number> \
  --query 'KeyMetadata.{Arn:Arn,Account:AWSAccountId,Enabled:Enabled,State:KeyState,Manager:KeyManager}' \
  --output table
aws kms get-key-policy \
  --key-id alias/<student-number> \
  --policy-name default \
  --output text
```

### Checkpoint 3: KMS and pycryptodome round trips — 1 mark

Open both encryption scripts. One script must use your KMS key to encrypt and decrypt data. The other must perform a separate local AES encryption and decryption with `pycryptodome`. Both scripts must produce encrypted and decrypted output rather than copy the original file.

Run both scripts live. Show that each decrypted result matches the original by printing the comparison, displaying the original and decrypted text, or opening the files. If your scripts do not show a clear comparison, use your actual filenames with:

```bash
cmp <original-file> <kms-decrypted-file> && echo "KMS match"
cmp <original-file> <aes-decrypted-file> && echo "AES match"
```

Refresh your Lab 4 bucket in the S3 Console and show the original, encrypted, and decrypted objects produced by the scripts.

### Cleanup — 0.5-mark deduction if incomplete

Clean up only after the facilitator completes all three checkpoints:

1. Open AWS Console → S3 → Buckets, select the Lab 4 bucket, choose **Empty**, and then choose **Delete**.
2. Open AWS Console → KMS → Customer managed keys, select your Lab 4 key, and choose **Key actions → Schedule key deletion**.

Cleanup is complete when the S3 bucket is absent and the KMS key is `PendingDeletion`. No later lab requires this bucket or key.
