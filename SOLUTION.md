# IAM Policies and Roles - Advanced Identity Management

## Part 1: Understanding IAM Policy Structure

### Version
This defines the policy language version used by AWS. It is usually set to "2012-10-17".

### Statement
This is the main section of the policy. It contains one or more permission rules.

### Sid
This is an optional statement ID. It helps label and organize individual statements inside the policy.

### Effect
This defines whether the action is allowed or denied. The value can be "Allow" or "Deny".

### Action
This specifies which AWS actions are being controlled, such as s3:GetObject or ec2:StartInstances.

### Resource
This defines which AWS resource the action applies to. It is usually written as an ARN.

---

## Part 2: Custom IAM Policies

Three custom IAM policies were created:

- S3-ReadOnly-SpecificBucket  
  Allows listing a bucket and reading objects from a specific S3 bucket.

- EC2-StartStop-Only  
  Allows describing, starting, stopping, and rebooting EC2 instances, while explicitly denying termination.

- CloudWatch-Logs-Write-Only  
  Allows creating log groups, log streams, and writing logs to CloudWatch.

---

## Part 3: Attach Policy to User

The S3 read-only policy was attached to a test user.

This ensures the user can access data in the specified S3 bucket but cannot modify it.

---

## Part 4: IAM Roles

The following roles were created:

- EC2-S3-ReadOnly-Role  
  Allows EC2 instances to access S3 without storing credentials.

- Lambda-Basic-Execution-Role  
  Allows Lambda functions to write logs to CloudWatch.

- CrossAccount-ReadOnly-Role  
  Allows another AWS account to assume the role using an external ID.

---

## Part 5: Test with AWS CLI

### Command Used

echo "test" > test.txt  
aws s3 cp test.txt s3://ce-demo-01/

### Result

upload: ./test.txt to s3://ce-demo-01/test.txt

### Interpretation

The upload worked successfully. This means the current AWS CLI user has write permissions to the S3 bucket.

---

## Part 6: Least Privilege

A new IAM policy with conditions was created to restrict access.

The policy allows S3 read access only under a specific condition:

- Action: s3:GetObject  
- Resource: arn:aws:s3:::ce-demo-01/*  
- Condition: Access is limited based on the source IP  

This demonstrates the principle of least privilege by granting only the minimum permissions required and adding an additional restriction layer.

---

## Part 7: Troubleshooting

Commands used:

aws iam get-role --role-name EC2-S3-ReadOnly-Role  

aws iam simulate-principal-policy --policy-source-arn arn:aws:iam::170638199494:user/test-developer --action-names s3:GetObject --resource-arns arn:aws:s3:::ce-demo-01/test.txt  

### Results

- The get-role command worked successfully and confirmed that the EC2-S3-ReadOnly-Role exists.  
- The simulate-principal-policy command returned NoSuchEntity, which means the specified IAM user could not be found.  

### Conclusion

This troubleshooting step shows how to verify role configuration and identify problems caused by an incorrect or missing IAM entity name.