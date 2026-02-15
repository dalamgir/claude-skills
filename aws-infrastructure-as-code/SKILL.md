---
name: aws-infrastructure-as-code
description: Build well-architected AWS infrastructure with CDK and CloudFormation. Use when creating CDK applications, validating CloudFormation templates, checking security compliance with cfn-guard, troubleshooting failed deployments, or searching AWS IaC documentation. Supports TypeScript, Python, Java, C#, and Go. Includes CDK best practices, code samples, and CloudFormation resource validation.
metadata:
  author: AWS
  version: 1.0.0
  category: infrastructure
  tags: [aws, cdk, cloudformation, iac, infrastructure, validation, compliance, troubleshooting, cfn-lint, cfn-guard]
---

# AWS Infrastructure as Code

Build well-architected AWS infrastructure with CDK and CloudFormation using latest documentation, best practices, and code samples. Validate CloudFormation templates, check security compliance, and troubleshoot deployments.

## When to Use This Skill

Use this skill when you need to:
- Create or update CDK applications in TypeScript, Python, Java, C#, or Go
- Search for CDK constructs, patterns, and working examples
- Validate CloudFormation template syntax and schema
- Check security compliance against AWS Guard Rules
- Troubleshoot failed CloudFormation deployments
- Access latest CDK and CloudFormation documentation
- Learn CDK best practices and coding patterns
- Find region-specific resource availability

Do NOT use this skill for:
- General AWS service questions (use AWS documentation skills)
- Runtime AWS operations (use AWS CLI or SDK skills)
- Non-IaC infrastructure management

## Prerequisites

**CRITICAL:** Before using this skill, ensure:

**Most features work without AWS credentials:**
- CDK documentation search and code samples
- Writing CDK code
- Synthesizing CDK to CloudFormation (`cdk synth`)
- CloudFormation template validation (cfn-lint)
- Security compliance checking (cfn-guard)

**AWS credentials required only for:**
- Deploying CDK applications (`cdk deploy`)
- Troubleshooting failed CloudFormation deployments

**Required tools:**
- AWS CDK CLI: `npm install -g aws-cdk`
- Python 3.8+ (for validation tools)

## CDK Development Workflow

Follow this workflow when building AWS infrastructure with CDK:

### 1. Research and Design

**IMPORTANT:** Always search for constructs and patterns before writing code.

Search for constructs, patterns, and working examples to understand best practices and proven implementations.

**Example searches:**
- "Lambda function with DynamoDB"
- "VPC with public and private subnets"
- "API Gateway with Lambda integration"
- "S3 bucket with encryption and versioning"

### 2. Apply Best Practices

Learn recommended patterns before implementing:
- Prefer L2 constructs, use L3 for patterns, avoid L1 unless necessary
- Follow language-specific idioms (TypeScript, Python, Java, C#, Go)
- Apply CDK-NAG security checks from the start
- Use meaningful construct IDs and logical names

### 3. Write CDK Code

Implement your infrastructure using the patterns and examples from steps 1-2.

### 4. Synthesize and Validate

**CRITICAL:** Always synthesize your CDK code first to validate it generates valid CloudFormation:

```bash
# Synthesize CDK code to CloudFormation template
cdk synth

# Or save to file for validation
cdk synth > template.yaml
```

This validates your CDK code compiles correctly and generates valid CloudFormation.

Then validate the full generated template:
- Validate syntax and schema with cfn-lint
- Check security compliance with cfn-guard
- Get pre-deployment validation guidance

### 5. Deploy

```bash
cdk deploy
```

### 6. Troubleshoot (If Needed)

If deployment fails, use intelligent failure analysis with pattern matching against 30+ known failure cases.

## CDK Code Examples

### Basic CDK Application Structure

**TypeScript:**
```typescript
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as dynamodb from 'aws-cdk-lib/aws-dynamodb';

export class MyStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // DynamoDB table
    const table = new dynamodb.Table(this, 'MyTable', {
      partitionKey: { name: 'id', type: dynamodb.AttributeType.STRING },
      billingMode: dynamodb.BillingMode.PAY_PER_REQUEST,
      encryption: dynamodb.TableEncryption.AWS_MANAGED,
    });

    // Lambda function
    const fn = new lambda.Function(this, 'MyFunction', {
      runtime: lambda.Runtime.NODEJS_18_X,
      handler: 'index.handler',
      code: lambda.Code.fromAsset('lambda'),
      environment: {
        TABLE_NAME: table.tableName,
      },
    });

    // Grant permissions
    table.grantReadWriteData(fn);
  }
}
```

**Python:**
```python
from aws_cdk import (
    Stack,
    aws_lambda as lambda_,
    aws_dynamodb as dynamodb,
)
from constructs import Construct

class MyStack(Stack):
    def __init__(self, scope: Construct, id: str, **kwargs):
        super().__init__(scope, id, **kwargs)

        # DynamoDB table
        table = dynamodb.Table(
            self, "MyTable",
            partition_key=dynamodb.Attribute(
                name="id",
                type=dynamodb.AttributeType.STRING
            ),
            billing_mode=dynamodb.BillingMode.PAY_PER_REQUEST,
            encryption=dynamodb.TableEncryption.AWS_MANAGED,
        )

        # Lambda function
        fn = lambda_.Function(
            self, "MyFunction",
            runtime=lambda_.Runtime.PYTHON_3_11,
            handler="index.handler",
            code=lambda_.Code.from_asset("lambda"),
            environment={
                "TABLE_NAME": table.table_name,
            },
        )

        # Grant permissions
        table.grant_read_write_data(fn)
```

### Common CDK Patterns

#### VPC with Public and Private Subnets

```typescript
import * as ec2 from 'aws-cdk-lib/aws-ec2';

const vpc = new ec2.Vpc(this, 'MyVpc', {
  maxAzs: 2,
  natGateways: 1,
  subnetConfiguration: [
    {
      name: 'Public',
      subnetType: ec2.SubnetType.PUBLIC,
      cidrMask: 24,
    },
    {
      name: 'Private',
      subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS,
      cidrMask: 24,
    },
  ],
});
```

#### API Gateway with Lambda

```typescript
import * as apigateway from 'aws-cdk-lib/aws-apigateway';
import * as lambda from 'aws-cdk-lib/aws-lambda';

const api = new apigateway.RestApi(this, 'MyApi', {
  restApiName: 'My Service',
  description: 'This service serves my API.',
});

const handler = new lambda.Function(this, 'Handler', {
  runtime: lambda.Runtime.NODEJS_18_X,
  code: lambda.Code.fromAsset('lambda'),
  handler: 'index.handler',
});

const integration = new apigateway.LambdaIntegration(handler);
api.root.addMethod('GET', integration);
```

#### S3 Bucket with Encryption and Versioning

```typescript
import * as s3 from 'aws-cdk-lib/aws-s3';

const bucket = new s3.Bucket(this, 'MyBucket', {
  encryption: s3.BucketEncryption.S3_MANAGED,
  versioned: true,
  blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL,
  enforceSSL: true,
  removalPolicy: cdk.RemovalPolicy.RETAIN,
});
```

## CloudFormation Template Validation

### Syntax Validation with cfn-lint

Validates CloudFormation template syntax and schema:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Example template

Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-app-bucket
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
      VersioningConfiguration:
        Status: Enabled
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
```

**What cfn-lint checks:**
- Valid resource types and properties
- Required properties are present
- Property value types are correct
- Region-specific resource availability
- Deprecated resource types

### Security Compliance with cfn-guard

Checks templates against AWS security best practices:

**Common compliance rules:**
- S3 buckets must have encryption enabled
- S3 buckets must block public access
- Lambda functions must have IAM roles
- RDS instances must have encryption enabled
- Security groups must not allow 0.0.0.0/0 on sensitive ports
- IAM policies must not use wildcards for actions

**Example compliant template:**
```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
```

## Troubleshooting CloudFormation Deployments

### Common Failure Patterns

**IMPORTANT:** The troubleshooting tool matches against 30+ known failure patterns.

**Common issues:**

1. **Insufficient Permissions**
   - IAM role lacks required permissions
   - Service role trust policy incorrect
   - Resource policy conflicts

2. **Resource Conflicts**
   - Resource name already exists
   - Resource limit exceeded
   - Dependency cycle detected

3. **Configuration Errors**
   - Invalid property values
   - Missing required properties
   - Incompatible property combinations

4. **Network Issues**
   - VPC/subnet not found
   - Security group rules conflict
   - Route table misconfiguration

5. **Service Limits**
   - Account limit reached
   - Region-specific restrictions
   - Service quota exceeded

### Troubleshooting Workflow

1. **Get stack events** - Review CloudFormation stack events
2. **Analyze failure pattern** - Match against known patterns
3. **Check CloudTrail** - Deep dive into API calls (if enabled)
4. **Review resource configuration** - Validate resource properties
5. **Apply fix** - Update template or configuration
6. **Validate before redeploying** - Run cfn-lint and cfn-guard

## CDK Best Practices

### Application Configuration

- **Use context values** for environment-specific configuration
- **Parameterize stack names** for multi-environment deployments
- **Tag all resources** for cost allocation and organization
- **Use CDK Pipelines** for CI/CD automation

### Coding Best Practices

- **Prefer L2 constructs** over L1 (CloudFormation resources)
- **Use L3 patterns** for common architectures
- **Keep stacks focused** - one concern per stack
- **Use construct composition** for reusability
- **Follow language idioms** - TypeScript, Python, Java, C#, Go

### Construct Best Practices

- **Use meaningful IDs** - clear, descriptive construct IDs
- **Grant least privilege** - use grant methods, not inline policies
- **Validate inputs** - check construct properties at synthesis
- **Document constructs** - add comments and descriptions

### Security Best Practices

- **Enable encryption** by default for all data stores
- **Use CDK-NAG** for security compliance checks
- **Apply least privilege** IAM policies
- **Enable logging** for all resources
- **Use Secrets Manager** for sensitive data

### Testing Best Practices

- **Write unit tests** for construct logic
- **Use snapshot tests** for template validation
- **Test in non-production** before deploying to production
- **Use CDK assertions** for fine-grained testing

## Language-Specific Patterns

### TypeScript

```typescript
// Use interfaces for props
interface MyConstructProps {
  readonly tableName: string;
  readonly enableBackup?: boolean;
}

// Use readonly properties
export class MyConstruct extends Construct {
  public readonly table: dynamodb.Table;

  constructor(scope: Construct, id: string, props: MyConstructProps) {
    super(scope, id);
    
    this.table = new dynamodb.Table(this, 'Table', {
      tableName: props.tableName,
      pointInTimeRecovery: props.enableBackup ?? true,
    });
  }
}
```

### Python

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class MyConstructProps:
    table_name: str
    enable_backup: Optional[bool] = True

class MyConstruct(Construct):
    def __init__(self, scope: Construct, id: str, props: MyConstructProps):
        super().__init__(scope, id)
        
        self.table = dynamodb.Table(
            self, "Table",
            table_name=props.table_name,
            point_in_time_recovery=props.enable_backup,
        )
```

### Java

```java
public class MyConstruct extends Construct {
    private final Table table;

    public MyConstruct(final Construct scope, final String id, final MyConstructProps props) {
        super(scope, id);
        
        this.table = Table.Builder.create(this, "Table")
            .tableName(props.getTableName())
            .pointInTimeRecovery(props.getEnableBackup())
            .build();
    }

    public Table getTable() {
        return this.table;
    }
}
```

## Common Use Cases

### Serverless Application

```typescript
import * as cdk from 'aws-cdk-lib';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as apigateway from 'aws-cdk-lib/aws-apigateway';
import * as dynamodb from 'aws-cdk-lib/aws-dynamodb';

export class ServerlessStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // DynamoDB table
    const table = new dynamodb.Table(this, 'Table', {
      partitionKey: { name: 'id', type: dynamodb.AttributeType.STRING },
      billingMode: dynamodb.BillingMode.PAY_PER_REQUEST,
    });

    // Lambda function
    const handler = new lambda.Function(this, 'Handler', {
      runtime: lambda.Runtime.NODEJS_18_X,
      code: lambda.Code.fromAsset('lambda'),
      handler: 'index.handler',
      environment: {
        TABLE_NAME: table.tableName,
      },
    });

    table.grantReadWriteData(handler);

    // API Gateway
    const api = new apigateway.RestApi(this, 'Api', {
      restApiName: 'Serverless API',
    });

    const integration = new apigateway.LambdaIntegration(handler);
    api.root.addMethod('ANY', integration);
  }
}
```

### Container Application

```typescript
import * as ecs from 'aws-cdk-lib/aws-ecs';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import * as elbv2 from 'aws-cdk-lib/aws-elasticloadbalancingv2';

export class ContainerStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // VPC
    const vpc = new ec2.Vpc(this, 'Vpc', { maxAzs: 2 });

    // ECS Cluster
    const cluster = new ecs.Cluster(this, 'Cluster', { vpc });

    // Fargate Service
    const taskDefinition = new ecs.FargateTaskDefinition(this, 'TaskDef');
    
    taskDefinition.addContainer('web', {
      image: ecs.ContainerImage.fromRegistry('nginx'),
      portMappings: [{ containerPort: 80 }],
    });

    const service = new ecs.FargateService(this, 'Service', {
      cluster,
      taskDefinition,
    });

    // Load Balancer
    const lb = new elbv2.ApplicationLoadBalancer(this, 'LB', {
      vpc,
      internetFacing: true,
    });

    const listener = lb.addListener('Listener', { port: 80 });
    listener.addTargets('Target', {
      port: 80,
      targets: [service],
    });
  }
}
```

## Troubleshooting

### CDK Synthesis Fails

**Symptoms:**
- `cdk synth` command fails
- TypeScript/Python compilation errors
- Missing dependencies

**Solutions:**
1. Check for syntax errors in your code
2. Verify all imports are correct
3. Ensure dependencies are installed: `npm install` or `pip install -r requirements.txt`
4. Check CDK version compatibility: `cdk --version`

### CloudFormation Validation Errors

**Symptoms:**
- cfn-lint reports errors
- Invalid resource properties
- Missing required properties

**Solutions:**
1. Review cfn-lint output for specific errors
2. Check AWS documentation for resource properties
3. Verify property types and values
4. Ensure required properties are present

### Security Compliance Failures

**Symptoms:**
- cfn-guard reports violations
- Security best practices not followed
- Non-compliant resource configurations

**Solutions:**
1. Review cfn-guard output for specific violations
2. Enable encryption for data stores
3. Block public access for S3 buckets
4. Use least privilege IAM policies
5. Enable logging for resources

### Deployment Failures

**Symptoms:**
- `cdk deploy` fails
- CloudFormation stack in ROLLBACK state
- Resource creation errors

**Solutions:**
1. Check CloudFormation stack events
2. Review error messages for specific resources
3. Verify IAM permissions
4. Check service limits and quotas
5. Validate resource configurations
6. Use troubleshooting tool for pattern matching

### Permission Errors

**Symptoms:**
- Access denied errors
- Insufficient permissions
- IAM role issues

**Solutions:**
1. Verify AWS credentials: `aws sts get-caller-identity`
2. Check IAM policies for required permissions
3. Ensure service role trust policies are correct
4. Review resource policies for conflicts

## Quick Reference

### CDK Commands

```bash
# Initialize new project
cdk init app --language typescript

# Synthesize CloudFormation template
cdk synth

# Deploy stack
cdk deploy

# Destroy stack
cdk destroy

# List stacks
cdk list

# Show differences
cdk diff

# Bootstrap environment
cdk bootstrap
```

### Validation Commands

```bash
# Validate with cfn-lint
cfn-lint template.yaml

# Check compliance with cfn-guard
cfn-guard validate --data template.yaml --rules rules.guard

# Validate before deploy
aws cloudformation validate-template --template-body file://template.yaml
```

### Common CDK Imports

**TypeScript:**
```typescript
import * as cdk from 'aws-cdk-lib';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as dynamodb from 'aws-cdk-lib/aws-dynamodb';
import * as s3 from 'aws-cdk-lib/aws-s3';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import * as ecs from 'aws-cdk-lib/aws-ecs';
import * as rds from 'aws-cdk-lib/aws-rds';
import * as apigateway from 'aws-cdk-lib/aws-apigateway';
```

**Python:**
```python
from aws_cdk import (
    Stack,
    aws_lambda as lambda_,
    aws_dynamodb as dynamodb,
    aws_s3 as s3,
    aws_ec2 as ec2,
    aws_ecs as ecs,
    aws_rds as rds,
    aws_apigateway as apigateway,
)
```

## Best Practices Summary

### Do:
- Search for constructs and patterns before coding
- Apply CDK best practices from the start
- Always synthesize before deploying
- Validate templates with cfn-lint
- Check security compliance with cfn-guard
- Use L2 constructs over L1
- Grant least privilege permissions
- Enable encryption by default
- Tag all resources
- Test in non-production first

### Don't:
- Skip validation before deployment
- Use L1 constructs unless necessary
- Hardcode configuration values
- Grant overly permissive IAM policies
- Disable encryption
- Skip security compliance checks
- Deploy directly to production
- Ignore CloudFormation stack events
- Use wildcards in IAM policies
- Forget to enable logging

---

**Source:** AWS CDK and CloudFormation
**Documentation:** 
- https://docs.aws.amazon.com/cdk/
- https://docs.aws.amazon.com/cloudformation/
**Tools:** cfn-lint, cfn-guard
**License:** Apache 2.0
