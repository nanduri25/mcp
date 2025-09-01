---
description: Diagnose and resolve common errors with CloudFront distributions through systematic analysis and targeted remediation steps
---
# CloudFront Distribution Troubleshooting Guide

## Overview

This script guides you through diagnosing and resolving common errors with CloudFront distributions. It provides a systematic approach to troubleshooting common issues such as 403 Access Denied, 404 Not Found, and performance problems. By analyzing the distribution configuration, origin settings, and access controls, it helps pinpoint and fix issues without requiring advanced CloudFront expertise.

The script leverages AWS API MCP to analyze distribution settings, automatically detect common error patterns, and provides targeted remediation steps based on the specific error types encountered. It uses an issue-focused approach that prioritizes customer-friendly output with clear, actionable solutions.

## Parameters

- **distribution_id** (required): CloudFront distribution ID to troubleshoot
- **error_code** (optional): HTTP error code being returned (e.g., "403", "404", "504")
- **request_path** (optional): The specific path that's generating errors (e.g., "/images/logo.png")
- **request_domain** (optional): Domain name being used to access the distribution
- **active_validation** (optional, default: false): Whether to perform active checks that make API calls to origins
- **run_proactive_checks** (optional, default: true): Whether to perform proactive validation checks that identify potential issues before they cause errors

## Steps

### 1. Verify Dependencies

Check for required tools and warn the user if any are missing.

**Constraints:**
- You MUST verify the following tools are available in your context:
  - call_aws
  - suggest_aws_commands
  - tasklist
- You MUST ONLY check for tool existence and MUST NOT attempt to run the tools because running tools during verification could cause unintended side effects or consume resources unnecessarily
- You MUST inform the user about any missing tools
- You MUST ask if the user wants to proceed anyway despite missing tools
- You MUST respect the user's decision to proceed or abort

### 2. Validate Distribution ID and Collect Basic Information

Verify the distribution exists and gather configuration details.

**Constraints:**
- You MUST verify that the distribution_id is valid and exists because troubleshooting requires a valid distribution
- You MUST use AWS API MCP's `call_aws` tool to retrieve the distribution configuration
- You MUST extract and display key information including origin settings, behavior settings, and status because this information is essential for diagnosis
- You MUST create a simplified overview of the distribution that highlights key components in non-technical terms
- You MUST gracefully handle cases where the distribution doesn't exist or there are permission issues because these are common errors users encounter
- You SHOULD recommend checking for typos if the distribution_id is invalid to help users quickly correct their input
- You SHOULD create a configuration summary with the following categories:
  - Origin type and configuration (S3, custom origin, etc.)
  - Cache behavior settings (viewer protocol policy, allowed methods)
  - Security settings (restrictions, WebACL, etc.)
  - Distribution status and deployment information

### 3. Analyze Distribution Configuration Based on Error Code

Examine configuration details relevant to the specific error code using pattern recognition and correlation analysis.

**Constraints:**
- You MUST adapt your analysis based on the error_code parameter (if provided) because different errors have different root causes
- You MUST implement pattern-based error detection that matches the configuration against common error signatures
- You MUST use a decision tree approach that progressively narrows down potential causes based on error code and configuration state
- You MUST correlate settings across different parts of the distribution to identify incompatibilities
- For 403 errors, you MUST check origin access configuration, bucket policies (for S3 origins), and geo-restriction settings because these are common causes of access denied errors
- For 404 errors, you MUST check default root object, origin paths, and cache behavior patterns because these affect how resources are located
- For 5xx errors, you MUST check origin health, connection timeouts, and response timeout settings because these often cause server-side errors
- If no error_code is provided, you MUST perform a general configuration review focusing on common issues to provide valuable information regardless
- You MUST present findings in a clear, prioritized list of potential issues to help users focus on the most likely causes first
- You MUST categorize each issue by severity (Critical, High, Medium, Low) and likelihood to help users prioritize resolution efforts

### 4. Validate Origin Configuration

Check the origin settings for common misconfigurations using enhanced validation techniques.

**Constraints:**
- You MUST identify the origin type (S3, custom domain, load balancer, etc.) because different origin types have different potential issues
- You MUST implement origin-specific validation logic that checks for type-specific issues
- For S3 origins, you MUST check if OAC/OAI is properly configured and if the bucket exists because these are common causes of S3 origin issues
- You MUST verify bucket policy compatibility with the CloudFront access method
- For custom origins, you MUST verify the origin domain is valid and properly formatted because DNS and origin configuration issues are frequent problems
- You MUST check for protocol compatibility between CloudFront and the origin
- If active_validation is true, you SHOULD use AWS API MCP's `call_aws` to verify the origin is reachable because this can identify connectivity issues
- You MUST check for SSL/TLS mismatches between the origin and CloudFront because these cause security protocol failures
- You MUST explain any potential issues found in non-technical terms so users can understand the problems
- You MUST provide a compatibility matrix showing CloudFront settings and corresponding required origin configurations

### 5. Check Cache Behaviors and Request Handling

Analyze how requests are processed through cache behaviors using enhanced path analysis.

**Constraints:**
- You MUST check if the request_path (if provided) matches any path pattern in the cache behaviors because path pattern mismatches cause routing issues
- You MUST simulate request processing through the distribution to identify which cache behavior would handle the request
- You MUST analyze all cache behaviors that could potentially match the request pattern
- You MUST verify the viewer protocol policy is appropriate (HTTP/HTTPS settings) because protocol mismatches cause redirect or security issues
- You MUST check allowed HTTP methods for each relevant cache behavior because method restrictions can block legitimate requests
- You MUST analyze forwarded values configuration (query strings, headers, cookies) because these affect cache key generation and origin requests
- You MUST check if the request_path would be affected by any function associations because functions can modify request and response behavior
- You MUST visualize the request flow through cache behaviors using a clear step-by-step explanation
- You SHOULD provide a clear explanation of how the provided request_path would be processed to help users understand request flow

### 6. Examine Security Settings

Review security configurations that might be causing access issues, with enhanced focus on proactive security validation.

**Constraints:**
- You MUST check for WAF WebACL associations and determine if they might be blocking requests because WAF rules frequently cause access issues
- You MUST analyze geo-restriction settings that could block access based on location because geographic restrictions are a common cause of region-specific errors
- You MUST perform security posture assessment for common security misconfigurations
- You MUST check for field-level encryption settings that might affect response delivery because encryption configuration can cause compatibility issues
- You MUST verify HTTPS requirements that might prevent certain clients from connecting because protocol requirements can block older clients
- You MUST evaluate TLS version compatibility between clients, CloudFront, and origins
- You MUST clearly explain security settings in terms of their impact on user access so users understand the implications
- You MUST provide security configuration recommendations that balance security and accessibility

### 7. Generate Specific Remediation Steps

Provide a tailored set of steps to resolve identified issues, with enhanced focus on customer-friendly instructions.

**Constraints:**
- You MUST structure output in a tiered format with an executive summary followed by detailed information
- You MUST prioritize remediation steps based on likelihood of resolving the error because users should try the most promising solutions first
- You MUST categorize solutions by complexity and time required (Quick fix, Standard fix, Advanced solution)
- You MUST provide an estimated time and impact for each solution
- You MUST provide specific AWS CLI commands using AWS API MCP's `call_aws` syntax that would fix identified issues because this gives users actionable solutions
- You MUST provide both AWS Console instructions and CLI commands for each solution
- You MUST present the simplest, most direct solution first before offering more complex alternatives
- You MUST explain what each remediation step accomplishes so users understand the purpose of each action
- You MUST include verification steps to confirm the fix worked because users need to validate their changes
- You SHOULD warn about potential side effects of each remediation action because some changes may have unintended consequences
- You MUST adapt recommendations based on all previously gathered information to provide a comprehensive solution

### 8. Provide Additional Diagnostic Commands and Proactive Recommendations

Offer follow-up commands for further troubleshooting if needed and provide proactive recommendations to prevent future issues.

**Constraints:**
- You MUST provide AWS CLI commands using AWS API MCP's `call_aws` syntax for additional diagnostic checks because further investigation may be needed
- You MUST include commands to view CloudFront logs if logging is enabled because logs contain valuable diagnostic information
- You MUST suggest relevant CloudWatch metrics to monitor because metrics help identify performance issues
- You MUST recommend proactive monitoring and alerting configurations that would detect similar issues early
- You MUST provide best practice recommendations to prevent the identified issues from recurring
- You MUST suggest configuration improvements based on AWS best practices
- You MUST explain how to check origin health directly (if relevant) because origin health is critical for distribution functionality
- You MUST recommend performance optimizations based on the distribution's configuration
- You SHOULD organize commands by scenario (e.g., "If issue persists after trying steps above...") to guide users through progressive troubleshooting

## Examples

### Example 1: Troubleshooting 403 Forbidden with S3 Origin

```
You: My CloudFront distribution d123456abcdef is returning 403 errors when I try to access images.

AI: I'll help you troubleshoot those 403 errors with your CloudFront distribution d123456abcdef.

[AI retrieves distribution config and analyzes it using pattern recognition]

📋 DISTRIBUTION SUMMARY
Origin Type: Amazon S3 Bucket
Bucket: example-bucket.s3.amazonaws.com
Security: Public access blocked, No OAC/OAI configured
Cache Settings: Default TTL 86400s, Compression enabled
Status: Deployed (Last modified: 2025-05-22)

🚨 ISSUE IDENTIFIED: ACCESS DENIED (403)
--------------------------------
Root Cause: CloudFront cannot access your S3 bucket content because it lacks permission.

Your S3 bucket blocks all public access, but CloudFront hasn't been given special permission to access it.

⚡ QUICK FIX (Estimated time: 5 minutes):
1. Set up Origin Access Control (OAC) to allow CloudFront secure access to your private bucket

AWS Console Instructions:
1. Open CloudFront console
2. Select your distribution d123456abcdef
3. Go to Origins tab and edit your S3 origin
4. Under Origin Access, select "Origin access control settings (recommended)"
5. Create a new control setting or select an existing one
6. Save changes
7. Copy the suggested bucket policy and update your S3 bucket policy

AWS CLI Commands:
```
call_aws(cli_command="aws cloudfront create-origin-access-control --name 'OAC-example-bucket' --origin-access-control-config '{\"Description\": \"OAC for example-bucket\", \"SigningProtocol\": \"sigv4\", \"SigningBehavior\": \"always\", \"OriginAccessControlOriginType\": \"s3\"}')")
```

This will return an Origin Access Control ID. Then update your distribution:

```
call_aws(cli_command="aws cloudfront get-distribution-config --id d123456abcdef --output json > dist-config.json")
```

Edit the dist-config.json file to add the OAC ID to your S3 origin, then:

```
call_aws(cli_command="aws cloudfront update-distribution --id d123456abcdef --distribution-config file://dist-config.json --if-match ETAG_VALUE")
```

And update your bucket policy:

```
call_aws(cli_command="aws s3api put-bucket-policy --bucket example-bucket --policy '{\"Version\": \"2012-10-17\", \"Statement\": [{\"Sid\": \"AllowCloudFrontServicePrincipal\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"cloudfront.amazonaws.com\"}, \"Action\": \"s3:GetObject\", \"Resource\": \"arn:aws:s3:::example-bucket/*\", \"Condition\": {\"StringEquals\": {\"AWS:SourceArn\": \"arn:aws:cloudfront::ACCOUNT_ID:distribution/d123456abcdef\"}}}]}'")
```

✅ VERIFICATION STEPS:
1. Wait 5-10 minutes for changes to propagate
2. Try accessing your content through CloudFront again
3. If still seeing errors, check CloudFront logs with:
   ```
   call_aws(cli_command="aws cloudfront get-distribution --id d123456abcdef")
   ```

📈 PROACTIVE RECOMMENDATIONS:
1. Enable CloudFront logging to troubleshoot future issues more easily
2. Set up CloudWatch alarms for 4xx and 5xx error rates
3. Consider creating an invalidation if you're still seeing cached 403 errors
```

### Example 2: Troubleshooting 504 Gateway Timeout

```
You: My CloudFront distribution abc123def456 is giving 504 errors. The distribution uses a custom origin pointing to my ALB.

AI: I'll help you troubleshoot those 504 Gateway Timeout errors for your distribution abc123def456.

[AI analyzes the configuration using automated pattern recognition and correlation]

📋 DISTRIBUTION SUMMARY
Origin Type: Application Load Balancer (ALB)
Origin Domain: my-alb-12345.us-east-1.elb.amazonaws.com
Timeout Settings: Response timeout 30s (default), Connection timeout 10s (default)
Cache Settings: Default TTL 86400s, Compression enabled
Status: Deployed (Last modified: 2025-06-15)

🚨 ISSUE IDENTIFIED: GATEWAY TIMEOUT (504)
--------------------------------
Root Cause: Your ALB or its backend services are taking longer than 30 seconds to respond, causing CloudFront to time out.

This is commonly caused by:
1. Backend processing taking too long
2. Default CloudFront timeout settings being too short for your application
3. ALB health check failures or capacity issues

⚡ QUICK FIX (Estimated time: 5 minutes):
Increase the origin response timeout to give your ALB more time to respond

AWS Console Instructions:
1. Open CloudFront console
2. Select distribution abc123def456
3. Go to Origins tab and edit your ALB origin
4. Under Origin timeout, increase from 30 to 60 seconds
5. Save changes

AWS CLI Command:
```
call_aws(cli_command="aws cloudfront get-distribution-config --id abc123def456 --output json > dist-config.json")
```

Edit the dist-config.json file to increase the OriginReadTimeout value, then:

```
call_aws(cli_command="aws cloudfront update-distribution --id abc123def456 --distribution-config file://dist-config.json --if-match ETAG_VALUE")
```

⚠️ IMPORTANT: This solution addresses the symptom (timeout), but not the underlying cause (slow backend).

🔍 DEEPER INVESTIGATION (Recommended):
1. Check ALB metrics for high latency or errors:
   ```
   call_aws(cli_command="aws cloudwatch get-metric-statistics --namespace AWS/ApplicationELB --metric-name TargetResponseTime --dimensions Name=LoadBalancer,Value=my-alb-12345 --start-time 2025-06-15T00:00:00Z --end-time 2025-06-15T23:59:59Z --period 300 --statistics Average Maximum")
   ```

2. Check target group health:
   ```
   call_aws(cli_command="aws elbv2 describe-target-health --target-group-arn YOUR_TARGET_GROUP_ARN")
   ```

✅ VERIFICATION STEPS:
1. Wait 5-10 minutes for changes to propagate
2. Try accessing your content through CloudFront again
3. Monitor for continued 504 errors using:
   ```
   call_aws(cli_command="aws cloudwatch get-metric-statistics --namespace AWS/CloudFront --metric-name 5xxErrorRate --dimensions Name=DistributionId,Value=abc123def456 Name=Region,Value=Global --start-time 2025-06-15T00:00:00Z --end-time 2025-06-15T23:59:59Z --period 300 --statistics Average")
   ```

📈 PROACTIVE RECOMMENDATIONS:
1. Implement performance monitoring for your backend services
2. Set up CloudWatch alarms for response times exceeding 15 seconds
3. Consider implementing caching strategies to reduce origin load
4. Review your application for long-running operations that could be optimized
```

## Troubleshooting

### Distribution Is Still Deploying

If you see "Status: InProgress" in the distribution details, it means changes are still propagating to CloudFront edge locations. Wait until the status changes to "Deployed" before concluding that your remediation didn't work. You can check the current status with:

```
aws cloudfront get-distribution --id YOUR_DISTRIBUTION_ID
```

### Cache Persists After Configuration Change

Even after fixing the configuration, CloudFront may continue to serve previously cached errors for the duration of your TTL settings. To force an immediate update:

1. Create an invalidation to clear the cache:
```
aws cloudfront create-invalidation --distribution-id YOUR_DISTRIBUTION_ID --paths "/*"
```

2. Or update with a cache behavior with a version identifier in the path.

### Permissions Issues During Troubleshooting

If you encounter "AccessDenied" errors when running remediation commands, you may not have sufficient permissions. Verify your AWS credentials have the following permissions:
- cloudfront:GetDistribution
- cloudfront:GetDistributionConfig
- cloudfront:UpdateDistribution
- s3:GetBucketPolicy (for S3 origins)
- s3:PutBucketPolicy (for S3 origins)

### Preventing Future Issues

Implement these proactive measures to prevent common CloudFront issues:

1. **Regular Configuration Reviews**:
   Schedule periodic reviews of your CloudFront distributions against best practices

2. **Monitoring and Alerting**:
   Set up CloudWatch alarms for the following metrics:
   - 4xxErrorRate and 5xxErrorRate exceeding 1-5%
   - Origin latency exceeding 75% of your timeout values
   - Cache hit ratio dropping below expected levels

3. **Logging and Analysis**:
   - Enable CloudFront access logging to an S3 bucket
   - Consider setting up automated log analysis for error pattern detection

4. **Origin Health Checks**:
   - Implement regular health checks for origin servers
   - Configure fallback origins for critical distributions

5. **Configuration as Code**:
   - Manage CloudFront distributions with infrastructure as code (CloudFormation, CDK, etc.)
   - Implement pre-deployment validation in your CI/CD pipeline