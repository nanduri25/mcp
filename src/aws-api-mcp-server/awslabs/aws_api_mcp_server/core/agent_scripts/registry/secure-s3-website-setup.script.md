---
description: Set up a secure static website using Amazon CloudFront with an S3 bucket as the origin, implementing AWS security best practices
---
# Secure S3 Website with CloudFront Setup

## Overview

This script guides you through setting up a secure static website using Amazon CloudFront with an S3 bucket as the origin. The implementation follows AWS security best practices, including Origin Access Control (OAC) to prevent direct access to the S3 bucket, HTTPS-only access, and proper bucket policies.

The script leverages the AWS API MCP to execute CloudFront and S3 API operations while providing domain-specific guidance and error handling. It offers a more streamlined and security-focused approach than using the AWS Console or basic CLI commands directly.

## Parameters

- **bucket_name** (required): Name for the S3 bucket that will host website content
- **region** (optional, default: "us-west-2"): AWS region to deploy resources in
- **price_class** (optional, default: "PriceClass_100"): CloudFront price class (PriceClass_100, PriceClass_200, or PriceClass_All)
- **default_root_object** (optional, default: "index.html"): The object to serve when the root URL is requested
- **enable_compression** (optional, default: true): Whether to enable compression for the distribution
- **include_default_content** (optional, default: true): Whether to include default HTML and CSS content

## Steps

### 1. Verify Dependencies

Check for required tools and warn the user if any are missing.

**Constraints:**
- You MUST verify the following tools are available in your context:
  - call_aws
  - suggest_aws_commands
  - tasklist
  - write_file
  - read_file
- You MUST ONLY check for tool existence and MUST NOT attempt to run the tools because running tools during verification could cause unintended side effects or consume resources unnecessarily
- You MUST inform the user about any missing tools
- You MUST ask if the user wants to proceed anyway despite missing tools
- You MUST respect the user's decision to proceed or abort

### 2. Validate Parameters and Environment

Verify parameters, check permissions, and validate the AWS environment.

**Constraints:**
- You MUST verify that the bucket_name follows S3 naming rules (lowercase, no underscores, 3-63 characters, etc.)
- You MUST check if the bucket already exists using AWS API MCP because creating a bucket with a name that already exists will fail
- You MUST verify that AWS credentials have sufficient permissions for S3 and CloudFront operations because insufficient permissions will cause operations to fail later in the process
- You MUST provide clear error messages if any validation fails so the user can address the issues
- You SHOULD suggest alternative bucket names if the requested name is invalid or already exists to help the user proceed
- You MUST check that the region is valid because operations will fail with invalid regions

### 3. Create S3 Bucket

Create the S3 bucket that will host the website content.

**Constraints:**
- You MUST create a bucket in the specified region with default settings
- You MUST use AWS API MCP's `call_aws` tool to execute the bucket creation
- You MUST ensure the bucket is not publicly accessible because public access creates security vulnerabilities
- You MUST handle cases where bucket creation fails and provide helpful error messages
- You MUST NOT enable static website hosting on the bucket because access will be exclusively through CloudFront for improved security

### 4. Generate and Upload Website Content

Create default website files and upload them to the S3 bucket if requested.

**Constraints:**
- You MUST respect the include_default_content parameter
- If include_default_content is true, you MUST create basic HTML and CSS files that clearly demonstrate a CloudFront-hosted website
- You MUST upload files with the correct content types (text/html, text/css) because incorrect content types can cause rendering issues in browsers
- You MUST use AWS API MCP's `call_aws` tool for uploading files
- You MUST handle potential upload failures gracefully
- You SHOULD ask the user if they want to customize the content before uploading

### 5. Create Origin Access Control (OAC)

Set up CloudFront Origin Access Control to secure access to the S3 bucket.

**Constraints:**
- You MUST create an OAC with sigv4 protocol and "always" signing behavior because this provides the most secure access method
- You MUST use AWS API MCP's `call_aws` tool to create the OAC
- You MUST store the OAC ID for use in subsequent steps because it's required for the CloudFront distribution configuration
- You MUST explain to the user why OAC is being used instead of legacy Origin Access Identity (OAI) since OAC is the newer and more secure method

### 6. Create CloudFront Distribution

Create a CloudFront distribution with secure settings.

**Constraints:**
- You MUST create a distribution using the S3 bucket as the origin with the OAC ID
- You MUST set ViewerProtocolPolicy to redirect-to-https to enforce HTTPS because HTTPS provides encrypted connections for security
- You MUST set up caching behavior with compression enabled if requested
- You MUST use the specified price_class and default_root_object parameters
- You MUST use AWS API MCP's `call_aws` tool to create the distribution
- You MUST store the distribution ID and domain name for later use
- You MUST explain that the distribution deployment will take 5-15 minutes to complete so the user has appropriate expectations

### 7. Configure S3 Bucket Policy

Update the S3 bucket policy to allow access only from the CloudFront distribution.

**Constraints:**
- You MUST create a bucket policy that restricts access to CloudFront only using the specific distribution ARN because this prevents direct access to bucket objects
- You MUST use AWS API MCP's `call_aws` tool to update the bucket policy
- You MUST ensure the policy prevents direct public access to the bucket content because this is a security best practice
- You MUST explain the security benefit of this policy to the user so they understand why it's important

### 8. Validate and Test the Setup

Verify that the CloudFront distribution is working correctly.

**Constraints:**
- You MUST check the CloudFront distribution deployment status
- You MUST explain how to test that the website is working once the distribution is deployed
- You MUST provide the CloudFront domain URL for testing
- You MUST create a simple HTML document explaining how to test the distribution
- You SHOULD suggest setting up custom domain and HTTPS certificate as potential next steps

## Examples

### Example 1: Basic Usage

```
You: I want to set up a secure S3 website with CloudFront.

AI: I'll help you set up a secure S3 website using CloudFront. First, I need a name for your S3 bucket.

You: Use my-secure-website-bucket

AI: Great! I'll set up a secure CloudFront distribution with S3 bucket 'my-secure-website-bucket'.
[AI proceeds with steps 1-8, creating resources and providing updates]

Your secure CloudFront website has been set up! The website will be available at:
https://d1a2b3c4d5e6f7.cloudfront.net/

Note: It may take 5-15 minutes for the CloudFront distribution to fully deploy.
```

### Example 2: Custom Region and Price Class

```
You: I want to set up a secure S3 website with CloudFront in the EU and with global distribution.

AI: I'll help you set up a secure S3 website using CloudFront. First, I need some information:

What would you like to name your S3 bucket?

You: europe-website-2025

AI: Great! I'll use 'europe-website-2025' as your bucket name. I'll set up the resources in the eu-west-1 region with global distribution (PriceClass_All).
[AI proceeds with steps 1-8, creating resources in eu-west-1 with PriceClass_All]

Your secure CloudFront website has been set up! The website will be available at:
https://a1b2c3d4e5f6g7.cloudfront.net/

Note: It may take 5-15 minutes for the CloudFront distribution to fully deploy, and since you've chosen global distribution (PriceClass_All), your content will be available at edge locations worldwide.
```

## Troubleshooting

### Bucket Already Exists

If you encounter a "BucketAlreadyExists" error, it means the bucket name is already taken by another AWS account. S3 bucket names must be globally unique across all AWS accounts. Try a different bucket name with additional unique identifiers like timestamps or random strings.

### Distribution Deployment Time

CloudFront distributions typically take 5-15 minutes to deploy. During this time, the distribution status will show as "InProgress". You can check the status using the AWS CLI command:
```
aws cloudfront get-distribution --id YOUR_DISTRIBUTION_ID
```

### Access Denied When Testing

If you receive "Access Denied" errors when trying to access your CloudFront URL:
1. Verify that the bucket policy has been correctly applied
2. Check that the Origin Access Control is properly configured
3. Ensure that the files were uploaded to the S3 bucket with the correct path
4. Confirm that the CloudFront distribution has completed deployment

### Invalid Bucket Name

S3 bucket names must follow these rules:
- Between 3 and 63 characters long
- Only lowercase letters, numbers, dots (.), and hyphens (-)
- Must start and end with a letter or number
- Cannot be formatted as an IP address
- Cannot start with the prefix 'xn--'
- Cannot start with the prefix 'sthree-'
- Cannot start with the prefix 'sthree-configurator'
- Cannot start with the prefix 'aws-'