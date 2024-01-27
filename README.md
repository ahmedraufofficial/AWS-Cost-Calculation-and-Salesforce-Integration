# AWS Cost Calculation and Salesforce Integration

## Scope

This project focuses on using AWS CloudFormation and Terraform to set up infrastructure on AWS, calculate the cost of all services using an AWS Lambda function, create an API endpoint in Salesforce for cost data reception, implement a CI/CD pipeline with Bitbucket Pipeline, and configure monitoring and alerting for the Lambda function. Additionally, it involves scheduling the Lambda function to run at specific intervals.

## Solution

### Initial Bitbucket Pipeline YAML Setup
The Bitbucket Pipeline utilizes the Docker image "hashicorp/terraform:light." The pipeline, named "default," includes a single step, "Checking TF State," which performs actions such as installing the curl command-line tool, creating a variables.tf file, and checking and managing Terraform state.

### Main Terraform Setup
The Terraform configuration script provisions and manages AWS resources, including provider configuration, S3 bucket creation, S3 object creation, and AWS CloudFormation stack creation.

### AWS Provider Configuration

```
provider "aws" {
  region     = "us-east-1"
  access_key = "${var.access_key}
  ```"
  secret_key = "${var.secret_key}
  ```"
}
```
S3 Bucket Resource

```
resource "aws_s3_bucket" "python_bucket" {
  bucket = "${var.library_bucket_name}"
}
```
S3 Bucket Object Resource

```
resource "aws_s3_bucket_object" "object" {
  bucket = aws_s3_bucket.python_bucket.id
  key    = "shell-lambda-package.zip"
  source = "${path.module}
  /shell-lambda-package.zip"

  etag = filemd5("${path.module}
  /shell-lambda-package.zip")
}
```
## AWS CloudFormation Stacks

### Two CloudFormation stack resources are defined:

- "sf_secret_stack": Deploys a stack for Salesforce secret creation.
- "lambda_stack": Deploys a stack for Lambda function and AWS resources creation.

### Salesforce Secret Stack

Template Parameters

- SalesforcePassword (String)
- SalesforceUsername (String)
- SalesforceToken (String)

### Resources

- SalesforceSecret (AWS::SecretsManager::Secret): Creates a Secrets Manager secret for storing Salesforce credentials.

### Outputs

- SalesforceSecretArn: Exports the ARN of the SalesforceSecret resource.
- Lambda Stack

### Template Parameters

- SalesforceBucketName (String)
- LibraryBucketName (String)

### Resources

Various AWS resources are defined, enabling the processing of AWS Cost and Usage Reports and their integration with Salesforce.

### Function Explanation

The MyFunction Lambda function processes AWS Cost and Usage Reports stored in the designated S3 bucket, sending data to Salesforce. The function retrieves Salesforce credentials from AWS Secrets Manager.

This CloudFormation template automates the setup and configuration of these AWS resources, allowing for the automated processing of AWS Cost and Usage Reports and their integration with Salesforce.

Users need to provide values for parameters such as SalesforceBucketName and LibraryBucketName for customization. Correct AWS credentials and permissions are crucial for executing this CloudFormation stack.
