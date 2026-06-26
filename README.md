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
  * Bucket: `source-replica07` (general purpose, versioning enabled).
  * S3 replication rule configured on this bucket. 

* Destination account (Account B)
  * Region:  Mumbai `ap-south-1` (same as source).
  * Bucket: `destination-replica007` (general purpose, versioning enabled).
  * Bucket policy granting the replication role permissions to replicate objects.

* IAM service role
  * Automatically created by S3 when the replication rule is saved, with a name like `s3crr_role_for_source-replica07_3`.
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

## Step-by-step implementation
## 1. Create the Source Bucket (Account A)

1. Sign in to the **AWS Management Console** using **Account A** and open **Amazon S3**.

2. Click **Create bucket** and configure the following:
   - **Bucket type:** `General purpose`
   - **Region:** `Mumbai ap-south-1`
   - **Bucket name:** `source-replica07` (must be globally unique)

3. Leave **Block Public Access** enabled.

4. Scroll down, enable **Bucket Versioning**, and click **Create bucket**.

> **Result:** A versioned bucket named `source-replica07` is created in **Account A** and will serve as the source bucket for replication.

## 2. Create the Destination Bucket (Account B)

1. Sign in to the **AWS Management Console** using **Account B** and open **Amazon S3**.

2. Click **Create bucket** and configure the following:
   - **Bucket type:** `General purpose`
   - **Region:** `Mumbai ap-south-1` (same Region as the source bucket)
   - **Bucket name:** `destination-replica007`

3. Leave **Block Public Access** enabled and click **Create bucket**.

> **Result:** A general purpose destination bucket named `destination-replica007` is created in **Account B** in the same Region as the source bucket.

## 3. Configure the Replication Rule on the Source Bucket

In **Account A**:

1. Open **Amazon S3**, go to the **Buckets** list, and select the `source-replication07` bucket.

2. Navigate to the **Management** tab and locate the **Replication rules** section (initially empty).

3. Click **Create replication rule**.

4. Under **Replication rule configuration**, configure the following:
   - **Rule Name:** `different-account07`
   - **Status:** `Enabled`
   - **Priority:** `0`

5. Under **Source bucket**, confirm the bucket name is `source-replica07` and select **Apply to all objects in the bucket** so that all objects are replicated.

## 4. Choose the Destination Bucket and Account

Still in the replication rule wizard in **Account A**:

1. In the **Destination** section, select **Specify a bucket in another account**.

2. Provide the following details:
   - **Account ID:** The numeric AWS Account ID for **Account B**.
   - **Bucket Name:** `destination-replica007`
   - **Destination Region:** Automatically set to `ap-south-1`.

3. Enable **Change object ownership to destination bucket owner** so that replicated objects are fully owned and managed by **Account B**.

> **Note:** This ensures that even if the source account does not own the original object, the replicated object in the destination bucket is owned by the destination account.

## 5. Create the IAM Role for S3 Replication

1. In the **IAM role** section of the replication rule configuration, choose **Create new role**.

2. Amazon S3 automatically creates a service role (for example, `s3crr_role_for_source-replica07_3 `) with permissions to:
   - Read objects from the source bucket.
   - Write replicated objects to the destination bucket.

3. Click **Save** to create the replication rule.

### Replication Configuration Summary

- **Rule Name:** `different-account07`
- **Status:** `Enabled`
- **Destination:** `s3://destination-replica007`
- **Region:** `ap-south-1`
- **Scope:** `Entire bucket`
- **Replica Owner:** `Destination bucket owner`
- **IAM Role:** `s3crr_role_for_source-replica07_3`

> **Result:** Amazon S3 creates the required IAM service role and enables automatic replication of objects from the source bucket to the destination bucket.
## 6. Create the Destination Bucket Policy

In **Account B**, the destination bucket must trust the replication role from **Account A** so it can write replicated objects.

1. Open the **AWS Policy Generator** and select **S3 Bucket Policy** as the policy type.

2. Create a policy statement with the following configuration:
   - **Effect:** `Allow`
   - **Principal:** The ARN of the replication IAM role from **Account A**, for example:
     ```text
     arn:aws:iam::<SOURCE_ACCOUNT_ID>:role/service-role/s3crr_role_for_source-replica07_3
     ```
   - **Actions:**
     - `s3:ReplicateDelete`
     - `s3:ReplicateObject`
   - **Resource:**
     ```text
     arn:aws:s3:::destination-replica007
     ```
     *(Optionally, include object keys if required.)*

3. Generate the policy JSON and copy it.

4. In **Account B**, open the `destination-replica007` bucket, navigate to **Permissions** → **Bucket policy**, paste the generated JSON, and click **Save changes**.

> **Result:** The destination bucket policy allows the S3 replication IAM role from **Account A** to replicate objects into the destination bucket.

This bucket policy allows the S3 replication role from Account A to replicate objects into the destination bucket in Account B.

## Verification and Testing

- Upload a test object to the `source-replica07` bucket in **Account A** and verify that the object is successfully replicated to the `destination-replica007` bucket in **Account B**.

- Navigate to the **Replication rules** section of the source bucket and confirm that the replication rule status is **Enabled**.

- Review the IAM role `s3crr_role_for_source-replica07_3` to verify that its trust policy and permissions are correctly configured for Amazon S3 replication.

> **Expected Result:** Any new object uploaded to the source bucket is automatically replicated to the destination bucket, confirming that cross-account replication is working successfully.

