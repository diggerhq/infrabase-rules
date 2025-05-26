## Least-Privilege IAM Roles  
Grant only the specific actions and resources required—avoid any `"*"` wildcards.

## S3 Buckets Must Block Public Access  
Every `aws_s3_bucket` should set `block_public_acls = true`, `block_public_policy = true`, and use `acl = "private"`.
The only exception is buckets for storing website static data. If it is clear from the code and / or bucket name that this is the intended use of the bucket, it can be public - otherwise needs to be flagged. 

## Encrypt Data at Rest  
Ensure encryption is enabled for storage resources (e.g., S3 server-side encryption, KMS-encrypted EBS/RDS).

## No Hard-Coded Secrets  
Provider credentials and other sensitive values belong in variables or secret managers, never in plain‐text `.tf` files.
