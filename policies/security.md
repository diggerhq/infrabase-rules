## Least-Privilege IAM Roles  
Grant only the specific actions and resources required—avoid any `"*"` wildcards.

## S3 buckets may not use public-read or public-read-write ACLs
All aws_s3_bucket resources must set acl = "private" (or omit the attribute). Public access, if required, must be granted only through explicit bucket policies or CloudFront OAI. The only exception is buckets for storing website static data. If it is clear from the code and / or bucket name that this is the intended use of the bucket, it can be public - otherwise needs to be flagged. 

## Encrypt Data at Rest  
Ensure encryption is enabled for storage resources (e.g., S3 server-side encryption, KMS-encrypted EBS/RDS).

## No Hard-Coded Secrets  
Provider credentials and other sensitive values belong in variables or secret managers, never in plain‐text `.tf` files.

## S3 buckets must enable server-side encryption
Each aws_s3_bucket must define a server_side_encryption_configuration block using SSE-S3 or SSE-KMS.

## S3 buckets must enforce the Public Access Block controls
For every bucket, an accompanying aws_s3_bucket_public_access_block must set all four flags (block_public_acls, block_public_policy, ignore_public_acls, restrict_public_buckets) to true.

## IAM policies may not contain wildcards for both Action and Resource
Inline or attached policies must list only the specific actions and ARNs required; "*" on both fields is prohibited.

## Security groups may not allow unrestricted ingress from 0.0.0.0/0
Ingress rules that permit all protocols or all ports from the entire internet are disallowed. Narrow to required ports and CIDR ranges.

## Plain-text secrets are forbidden in Terraform code
Attribute values that match password or secret patterns (e.g., password = "...") must reference variables or external secret managers, not hard-coded literals.

## RDS instances must not be publicly accessible
aws_db_instance resources must set publicly_accessible = false unless explicitly exempted via an allow-list.

## RDS storage encryption is mandatory
Each RDS instance must set storage_encrypted = true; specify a KMS key where organizational policy requires.

## RDS deletions must retain a final snapshot
skip_final_snapshot must be false so that a backup is taken before instance deletion.

## Core AWS resources must be provisioned through approved modules
Direct use of raw aws_s3_bucket, aws_iam_*, aws_security_group, or aws_db_instance resources is disallowed; use organization-standard modules to ensure consistent tagging, logging, and security settings.
