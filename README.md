# aws-s3-cross_account-replication
## Overview
This project demonstrates the implementation of Amazon S3 Cross-Account Replication (CRR) using the AWS Management Console. The setup includes creating and configuring versioning on both source and destination S3 buckets, establishing a replication rule with the required IAM service role, and configuring a destination bucket policy to securely permit replication from a separate AWS account. This solution ensures automated, secure, and reliable replication of objects across AWS accounts for improved data redundancy, compliance, and disaster recovery.

## Project Goals
* Implement Amazon S3 Cross-Account Replication (CRR) to automatically replicate objects from a source S3 bucket in Account A to a destination S3 bucket in Account B.
* Enable bucket versioning on both source and destination buckets, which is a mandatory requirement for S3 replication.
* Configure an AWS Identity and Access Management (IAM) service role that Amazon S3 uses to securely read objects from the source bucket and replicate them to the destination bucket.
* Apply a destination bucket policy that grants the replication role the necessary permissions to perform replication-related S3 actions.
* Ensure secure, automated, and reliable data replication across AWS accounts to support data redundancy, backup, and disaster recovery requirements.

## Architecture
* Source account (Account A)
  * Region: Mumbai `ap-south-1.`
  * Bucket: source-replica07 (general purpose, versioning enabled).
  * S3 replication rule configured on this bucket. 

* Destination account (Account B)
  * Region:  Mumbai ap-south-1 (same as source).
  * Bucket: destination-replica007 (general purpose, versioning enabled).
  * Bucket policy granting the replication role permissions to replicate objects.

* IAM service role
  * Automatically created by S3 when the replication rule is saved, with a name like s3crr_role_for_source-replica07_3.
  * Trusted by S3 and allowed to read objects from the source bucket and write replicas into the destination bucket.
  
## Skills and concepts demonstrated
* **Amazon S3:** buckets, versioning, cross-account replication, replication rules, bucket policies.
* **AWS security:** least-privilege access via bucket policies and IAM service roles for S3 replication.
* **Cross-account access:** granting one account’s S3 role permissions to write objects into another account’s bucket.
* AWS Management Console navigation and configuration for S3 and IAM.

## Prerequisites
* Two AWS accounts (Account A as source, Account B as destination).
* Permission to create S3 buckets and configure replication in both accounts.
* Basic understanding of S3 versioning, IAM roles, and bucket policies.
