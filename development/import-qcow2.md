---
layout: default
title: AWS EC2 Import QCOW2 Image as Snapshot
parent: Development
nav_order: 7
---

# AWS EC2 Import QCOW2 Image as Snapshot

---

```shell
#MAC OS
brew install qemu

#check image file status
qemu-img info rpdsql-master-1000.qcow2

#convert QCOW2 to raw format
qemu-img convert rpdsql-master-1000.qcow2 rpdsql-master-1000.raw 

#upload image file into S3
aws s3 cp rpdsql-master-1000.raw s3://your-image-s3-bucket

#create vmimport role
aws iam create-role --role-name vmimport --assume-role-policy-document file://trust-policy.json

#set role policy
aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json

#create import snapshot task
aws ec2 import-snapshot --description "rpdsql-master-1000.qcow2" --disk-container file://container.json

#check import snapshot task status
aws ec2 describe-import-snapshot-tasks

#delete import snapshot task if long time not complete
aws ec2 cancel-import-task --import-task-id import-snap-06aa8a5584
```

trust-policy.json:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": { "Service": "vmie.amazonaws.com" },
         "Action": "sts:AssumeRole",
         "Condition": {
            "StringEquals":{
               "sts:Externalid": "vmimport"
            }
         }
      }
   ]
}
```

role-policy.json:

```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect": "Allow",
         "Action": [
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket" 
         ],
         "Resource": [
            "arn:aws:s3:::your-image-s3-bucket",
            "arn:aws:s3:::your-image-s3-bucket/*"
         ]
      },
      {
         "Effect": "Allow",
         "Action": [
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket",
            "s3:PutObject",
            "s3:GetBucketAcl"
         ],
         "Resource": [
            "arn:aws:s3:::export-bucket",
            "arn:aws:s3:::export-bucket/*"
         ]
      },
      {
         "Effect": "Allow",
         "Action": [
            "ec2:ModifySnapshotAttribute",
            "ec2:CopySnapshot",
            "ec2:RegisterImage",
            "ec2:Describe*"
         ],
         "Resource": "*"
      }
   ]
}
```

container.json:

```json
{
   "Description": "rpdsql-master-1000",
   "Format": "raw",
   "Url": "https://your-image-s3-bucket.amazonaws.com/rpdsql-master-1000.raw"
}
```

If you are using CentOS 7 image, please make sure it supports NVMeSSD (must be after CentOS 7.4). Otherwise, the AWS EC2 instance type must choose normal SSD support type, like M3 (otherwise, there will be an initialization error like cannot find volume and some script timeout).
