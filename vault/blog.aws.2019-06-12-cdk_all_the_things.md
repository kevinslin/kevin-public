---
id: 31bd869b-c88a-4b68-b775-047ac112110d
title: 'CDK All The Things: A Whirlwind Tour'
desc: ''
updated: 1608780756612
created: 1596416622315
tags:
  - cdk
  - devops
  - cloudformation
date: '2019-06-12'
categories: aws
category: aws
toc: true
header:
  teaser: /assets/images/20190612-header-conv.jpeg
excerpt: >-
  Ever wanted to provision AWS infrastructure with the expressivity of python,
  the type safety of java and the declarative nuance of CloudFormation?
layout: single
---

!["AWS Cloud Development Kit Representation"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190612-header-conv.jpeg)

Ever wanted to provision AWS infrastructure with the expressivity of python, the type safety of java and the declarative nuance of CloudFormation?

Well fear no more because the AWS Cloud Development Kit (CDK) is here. First [announced](https://aws.amazon.com/blogs/developer/aws-cdk-developer-preview/) in August of 2018, the CDK, simply put, is AWS's version of [Terraform](https://www.terraform.io/). The CDK enables users to use code to define and provision AWS infrastructure (the buzzword associated with this pattern is  known as [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code)). Whereas many IaC solutions take the form of config files (eg. CloudFormation) or a custom domain specific language (eg. terraform), the CDK allows you to use native programming languages. The CDK itself is written in [typescript](https://www.typescriptlang.org/) and uses the [jsii](https://github.com/awslabs/jsii) to create clients for other languages. Currently supported languages include  C#/.NET, Java, JavaScript, Python, and TypeScript

To be honest, I was skeptical when first hearing about the CDK. IaC on AWS was a crowded space and between 3rd party providers (terraform, troposphere, pulumi, etc) as well as AWS native alternatives (CloudFormation, SAM, etc), I wondered what utility yet another IaC would add. But I'm a firm believer in not disparaging something until giving it a try and what started of as a weekend of prototyping turned into months of heavy usage and deployments to production stacks. Yes, I shipped production code based on a library in developer preview and no, I have no regrets. By the end of this article, I hope to convince you why this might not be madness and why you might want to consider doing the same for your next project.

## CDK Setup

To start creating infrastructure via the CDK, download the CDK CLI. Use it to scaffold your initial project

```bash
npm i -g aws-cdk
mkdir hello-cdk
cd hello-cdk
cdk init app --language=typescript
```

This will create the following directory structure:

```bash
bin/
- hello-cdk.ts
lib/
- hello-cdk-stack.ts
node_modules/
cdk.json
package-lock.json
package.json
README.md
tsconfig.json
```

The action happens in `hello-cdk-stack.ts`.

```
import cdk = require('@aws-cdk/cdk');

export class HelloCdkStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
  }
}
```
A `cdk.Stack` represents a CloudFormation stack. You create infrastructure by creating `constructs` (explained in the next section) inside the stack.

## Constructs

Constructs are basic cloud components and can represent a single service (eg. S3 Bucket) or a set of services that represent a logical entity (eg. Code Pipeline with four stages). There is a construct for every AWS service that CloudFormation supports (which shockingly is still not every service - looking at you Amazon Connect...).

The CDK provides two levels of constructs: Low and High.

High level constructs let you get started quickly and will use sane default values for all unfufilled parameters. For example, take the VPC construct.

```typescript
import ec2 = require('@aws-cdk/aws-ec2');

export class HelloCdkStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
    const vpc = new ec2.Vpc(this, 'VPC');
  }
}
```

Without any values filled in, this automatically generates a public/private subnet pair in every AZ available in the region, along with the necessary routing tables, internet gateways and NAT instances to make everything just work. Of course this is highly configurable. Below is the same example with specific subnets.

```typescript
const vpc = new ec2.Vpc(this, 'TheVPC', {
  cidr: '10.0.0.0/21',
  subnetConfiguration: [
    {
      cidrMask: 24,
      name: 'Ingress',
      subnetType: SubnetType.Public,
    },
    {
      cidrMask: 24,
      name: 'Application',
      subnetType: SubnetType.Private,
    },
    {
      cidrMask: 28,
      name: 'Database',
      subnetType: SubnetType.Isolated,
    }
  ],
});
```

If you would like to have further control, you can drop down to low level constructs. Low level constructs are prefixed with `Cfn` and there is a direct one to one mapping between all the properties of a low level construct and the resulting CloudFormation.

Cloudformation definition of VPC

```yaml
Type: AWS::EC2::VPC
Properties:
  CidrBlock: String
  EnableDnsHostnames: Boolean
  EnableDnsSupport: Boolean
  InstanceTenancy: String
```


Typescript for CfnVpc
```typescript
const vpc = new ec2.CfnVPC(this, 'Resource', {
  cidrBlock: "10.0.0.0/16"
  enableDnsHostnames: true,
  enableDnsSupport: true,
  instanceTenancy: 'default'
});
```

## Provisioning a Service

To use a construct, you first need to add it to your project. Constructs are namespaced according to the following schema: `@aws-cdk/aws-{service_name}`

Lets start by provisioning a S3 bucket. First, add the `@aws-cdk/aws-s3` to the project.

```bash
yarn add @aws-cdk/aws-s3
```

Next, initialize the bucket in `hello-cdk-stack.ts`
```typescript
import cdk = require('@aws-cdk/cdk');
import { Bucket } from '@aws-cdk/aws-s3';

export class HelloCdkStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);
    const bucket = new Bucket(this, 'MyBucket');
  }
}
```

Because you are using typescript, you'll need to compile the code

```bash
tsc

# alternatively, run `tsc --watch` to have typescript
# watch the project and re-compile on every change
```

Run `cdk diff` to see what's changed
```
Resources
[+] AWS::S3::Bucket MyBucket MyBucketF68F3FF0
```

Run `cdk synthesize` to generate the CloudFormation

```yaml
Resources:
  MyBucketF68F3FF0:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
    Metadata:
      aws:cdk:path: HelloCdkStack/MyBucket/Resource
  CDKMetadata:
    Type: AWS::CDK::Metadata
    Properties:
      Modules: aws-cdk=0.33.0,@aws-cdk/aws-events=0.33.0,@aws-cdk/aws-iam=0.33.0,@aws-cdk/aws-kms=0.33.0,@aws-cdk/aws-s3=0.33.0,@aws-cdk/cdk=0.33.0,@aws-cdk/cx-api=0.33.0,@aws-cdk/region-info=0.33.0,jsii-runtime=node.js/v8.13.0
```

Run `cdk deploy` to create a CloudFormation deployment
```
HelloCdkStack: deploying...
HelloCdkStack: creating CloudFormation changeset...
 0/3 | 17:34:30 | CREATE_IN_PROGRESS   | AWS::CloudFormation::Stack | HelloCdkStack User Initiated
 0/3 | 17:34:34 | CREATE_IN_PROGRESS   | AWS::CDK::Metadata | CDKMetadata
 0/3 | 17:34:35 | CREATE_IN_PROGRESS   | AWS::S3::Bucket    | MyBucket (MyBucketF68F3FF0)
 0/3 | 17:34:36 | CREATE_IN_PROGRESS   | AWS::S3::Bucket    | MyBucket (MyBucketF68F3FF0) Resource creation Initiated
 0/3 | 17:34:37 | CREATE_IN_PROGRESS   | AWS::CDK::Metadata | CDKMetadata Resource creation Initiated
 1/3 | 17:34:37 | CREATE_COMPLETE      | AWS::CDK::Metadata | CDKMetadata
 2/3 | 17:34:57 | CREATE_COMPLETE      | AWS::S3::Bucket    | MyBucket (MyBucketF68F3FF0)
 3/3 | 17:34:58 | CREATE_COMPLETE      | AWS::CloudFormation::Stack | HelloCdkStack

 ✅  HelloCdkStack

Stack ARN:
arn:aws:cloudformation:us-west-2:*********:stack/HelloCdkStack/a9fe6ef0-8be0-11e9-a85b-06be1aa8968e
```

## Updating a Service
Now lets say you were using the bucket to store sensitive information and wanted to encrypt your bucket using a custom KMS key. Simply add the following line.

```typescript
const bucket = new Bucket(this, 'MyBucket', {
  encryption: BucketEncryption.Kms
});

```

Run `cdk diff` to see what changed

```bash
Stack HelloCdkStack
IAM Statement Changes
┌───┬─────────────────────┬────────┬────────────────────────────────────────────────────────────────┬────────────────────────────────────────────────────────────────┬───────────┐
│   │ Resource            │ Effect │ Action                                                         │ Principal                                                      │ Condition │
├───┼─────────────────────┼────────┼────────────────────────────────────────────────────────────────┼────────────────────────────────────────────────────────────────┼───────────┤
│ + │ ${MyBucket/Key.Arn} │ Allow  │ kms:CancelKeyDeletion                                          │ AWS:arn:${AWS::Partition}:iam::${AWS::AccountId}:root          │           │
│   │                     │        │ kms:Create*                                                    │                                                                │           │
│   │                     │        │ kms:Delete*                                                    │                                                                │           │
│   │                     │        │ kms:Describe*                                                  │                                                                │           │
│   │                     │        │ kms:Disable*                                                   │                                                                │           │
│   │                     │        │ kms:Enable*                                                    │                                                                │           │
│   │                     │        │ kms:Get*                                                       │                                                                │           │
│   │                     │        │ kms:List*                                                      │                                                                │           │
│   │                     │        │ kms:Put*                                                       │                                                                │           │
│   │                     │        │ kms:Revoke*                                                    │                                                                │           │
│   │                     │        │ kms:ScheduleKeyDeletion                                        │                                                                │           │
│   │                     │        │ kms:Update*                                                    │                                                                │           │
└───┴─────────────────────┴────────┴────────────────────────────────────────────────────────────────┴────────────────────────────────────────────────────────────────┴───────────┘
(NOTE: There may be security-related changes not in this list. See http://bit.ly/cdk-2EhF7Np)

Resources
[+] AWS::KMS::Key MyBucket/Key MyBucketKeyC17130CF
[~] AWS::S3::Bucket MyBucket MyBucketF68F3FF0
 └─ [+] BucketEncryption
     └─ {"ServerSideEncryptionConfiguration":[{"ServerSideEncryptionByDefault":{"KMSMasterKeyID":{"Fn::GetAtt":["MyBucketKeyC17130CF","Arn"]},"SSEAlgorithm":"aws:kms"}}]}
```

Before we continue, I would like to take a moment to point out how incredible `cdk diff` is. Updating production CloudFormation is one of the most nerve wracking things you can do (up there with bungee jumping, talking to strangers, and running raw SQL in production). This is why you want to take every precaution to check and verify potential changes before updating a CloudFormation template.

Prior to the CDK, you would have used [CloudFormation Changesets](https://aws.amazon.com/blogs/aws/new-change-sets-for-aws-cloudformation/). This is the CloudFormation native way of getting a diff - the equivalent of a `dry-run` option or a `terraform plan`.  To get a diff via a changesets, one would run the following commands.

```bash
STACK_NAME=HelloCdkStack
CHANGE_SET_NAME=AddKMSChangeSet
aws cloudformation create-change-set --stack-name $STACK_NAME \
    --template-body "$(cat template.yml)" \
    --capabilities CAPABILITY_NAMED_IAM  \
    --change-set-name $CHANGE_SET_NAME
aws cloudformation  describe-change-set --stack-name $STACK_NAME \
    --change-set-name $CHANGE_SET_NAME

# output

    "Changes": [
        {
            "Type": "Resource",
            "ResourceChange": {
                "Action": "Modify",
                "LogicalResourceId": "MyBucketF68F3FF0",
                "PhysicalResourceId": "hellocdkstack-mybucketf68f3ff0-16nzj9os1st5g",
                "ResourceType": "AWS::S3::Bucket",
                "Replacement": "False",
                "Scope": [
                    "Properties"
                ],
                "Details": [
                    {
                        "Target": {
                            "Attribute": "Properties",
                            "Name": "BucketEncryption",
                            "RequiresRecreation": "Never"
                        },
                        "Evaluation": "Dynamic",
                        "ChangeSource": "DirectModification"
                    },
                    {
                        "Target": {
                            "Attribute": "Properties",
                            "Name": "BucketEncryption",
                            "RequiresRecreation": "Never"
                        },
                        "Evaluation": "Static",
                        "ChangeSource": "ResourceAttribute",
                        "CausingEntity": "MyBucketKeyC17130CF.Arn"
                    }
                ]
            }
        },
        {
            "Type": "Resource",
            "ResourceChange": {
                "Action": "Add",
                "LogicalResourceId": "MyBucketKeyC17130CF",
                "ResourceType": "AWS::KMS::Key",
                "Scope": [],
                "Details": []
            }
        }
    ]
```


As you see, figuring out the `diff` by changeset required far more scripting and resulted in a lower fidelity diff than what was provided with `cdk diff`. Whereas the changeset only highlighted the creation of the CMK key and bucket encryption, `cdk diff` actually prints out the key and bucket policies.

Something else that's not obvious in the above script is that create-change-set is asynchronous and if you wanted to put it into a script, you would actually need to check that the change set had been created before being able to run `describe-change-set`. To do this properly, you would also need to check that a previous change-set with the name didn't already exist. You should also take into account that we are currently working with a very simple template - if the template had parameters, a changeset would require that every parameter be specified as well (even if parameter values haven't changed).

So long CLI short - `cdk diff` is better than sliced bread and it's hard to imagine ever being satisfied with native changesets again.

Okay, now that I've finished ranting about changesets, we can proceed to deploying changes by running `cdk deploy`. The CDK is good enough to flag changes that involve IAM and ask you for confirmation before proceeding.

```bash
cdk deploy

IAM Statement Changes
┌───┬─────────────────────┬────────┬───────────────────────────────────────────────────────┬───────────────────────────────────────────────────────┬───────────┐
│   │ Resource            │ Effect │ Action                                                │ Principal                                             │ Condition │
├───┼─────────────────────┼────────┼───────────────────────────────────────────────────────┼───────────────────────────────────────────────────────┼───────────┤
│ + │ ${MyBucket/Key.Arn} │ Allow  │ kms:CancelKeyDeletion                                 │ AWS:arn:${AWS::Partition}:iam::${AWS::AccountId}:root │           │
│   │                     │        │ kms:Create*                                           │                                                       │           │
│   │                     │        │ kms:Delete*                                           │                                                       │           │
│   │                     │        │ kms:Describe*                                         │                                                       │           │
│   │                     │        │ kms:Disable*                                          │                                                       │           │
│   │                     │        │ kms:Enable*                                           │                                                       │           │
│   │                     │        │ kms:Get*                                              │                                                       │           │
│   │                     │        │ kms:List*                                             │                                                       │           │
│   │                     │        │ kms:Put*                                              │                                                       │           │
│   │                     │        │ kms:Revoke*                                           │                                                       │           │
│   │                     │        │ kms:ScheduleKeyDeletion                               │                                                       │           │
│   │                     │        │ kms:Update*                                           │                                                       │           │
└───┴─────────────────────┴────────┴───────────────────────────────────────────────────────┴───────────────────────────────────────────────────────┴───────────┘
(NOTE: There may be security-related changes not in this list. See http://bit.ly/cdk-2EhF7Np)
Do you wish to deploy these changes (y/n)?
```

## Using Multiple Services
So now we've seen a simple example of creating a bucket and changing a property on said bucket. This is all good and healthy but where the CDK truly shines is when you use it to provision multiple services that need to talk to each other.  Lets say that you want to use a lambda to do some processing of data after an object is created in the bucket. Normally, to have a lambda access an encrypted S3 bucket, you would have to do the following:
1. Create a lambda and provision it via console/SAM/serverless/etc
2. Create an execution role for the lambda that is assumable by the lambda service
3. Attach a policy to aforementioned execution role that lets lambda write to cloudwatch logs
4. Attach a policy to aforementioned execution role that lets lambda perform the necessary `s3` operations scoped to the particular bucket
5. Attach a policy to aforementioned execution role that lets lambda perform the necessary `kms` operations scoped to the particular bucket
6. Update the KMS policy to allow access for the lambda principle

There's a lot involved here, and even if you use the console, steps 4-6 would still be operations you would have to perform manually. Lets see what this looks like when using the CDK.

```typescript
const bucket = new Bucket(this, 'MyBucket', {
  encryption: BucketEncryption.Kms
});

// lambda created with lambda basic execution role
const lambda = new Function(this, 'Lambda', {
  runtime: Runtime.NodeJS810,
  handler: 'index.handler',
  code: Code.asset('./assets/lambda_func/'),
  timeout: 30,
  memorySize: 576,
});

// this will add necessary permissions to the lambda service role
// AND the kms key policy
bucket.grantReadWrite(lambda.role);
```

Running a `cdk diff`, you now get the following:

```bash
The HelloCdkStack stack uses assets, which are currently not accounted for in the diff output! See https://github.com/awslabs/aws-cdk/issues/395
IAM Statement Changes
┌───┬───────────────────────────────────────────┬────────┬───────────────────────────────────────────┬─────────────────────────────────────────────┬───────────┐
│   │ Resource                                  │ Effect │ Action                                    │ Principal                                   │ Condition │
├───┼───────────────────────────────────────────┼────────┼───────────────────────────────────────────┼─────────────────────────────────────────────┼───────────┤
│ + │ ${Lambda/ServiceRole.Arn}                 │ Allow  │ sts:AssumeRole                            │ Service:lambda.${AWS::URLSuffix}            │           │
├───┼───────────────────────────────────────────┼────────┼───────────────────────────────────────────┼─────────────────────────────────────────────┼───────────┤
│ + │ ${MyBucket.Arn}                           │ Allow  │ s3:Abort*                                 │ AWS:${Lambda/ServiceRole}                   │           │
│   │ ${MyBucket.Arn}/*                         │        │ s3:DeleteObject*                          │                                             │           │
│   │                                           │        │ s3:GetBucket*                             │                                             │           │
│   │                                           │        │ s3:GetObject*                             │                                             │           │
│   │                                           │        │ s3:List*                                  │                                             │           │
│   │                                           │        │ s3:PutObject*                             │                                             │           │
├───┼───────────────────────────────────────────┼────────┼───────────────────────────────────────────┼─────────────────────────────────────────────┼───────────┤
│ + │ ${MyBucket/Key.Arn}                       │ Allow  │ kms:Decrypt                               │ AWS:${Lambda/ServiceRole.Arn}               │           │
│   │                                           │        │ kms:DescribeKey                           │                                             │           │
│   │                                           │        │ kms:Encrypt                               │                                             │           │
│   │                                           │        │ kms:GenerateDataKey*                      │                                             │           │
│   │                                           │        │ kms:ReEncrypt*                            │                                             │           │
│ + │ ${MyBucket/Key.Arn}                       │ Allow  │ kms:Decrypt                               │ AWS:${Lambda/ServiceRole}                   │           │
│   │                                           │        │ kms:DescribeKey                           │                                             │           │
│   │                                           │        │ kms:Encrypt                               │                                             │           │
│   │                                           │        │ kms:GenerateDataKey*                      │                                             │           │
│   │                                           │        │ kms:ReEncrypt*                            │                                             │           │
└───┴───────────────────────────────────────────┴────────┴───────────────────────────────────────────┴─────────────────────────────────────────────┴───────────┘
IAM Policy Changes
┌───┬───────────────────────┬────────────────────────────────────────────────────────────────────────────────┐
│   │ Resource              │ Managed Policy ARN                                                             │
├───┼───────────────────────┼────────────────────────────────────────────────────────────────────────────────┤
│ + │ ${Lambda/ServiceRole} │ arn:${AWS::Partition}:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole │
└───┴───────────────────────┴────────────────────────────────────────────────────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See http://bit.ly/cdk-2EhF7Np)

Parameters
[+] Parameter Lambda/Code/S3Bucket LambdaCodeS3Bucket65766E44: {"Type":"String","Description":"S3 bucket for asset \"HelloCdkStack/Lambda/Code\""}
[+] Parameter Lambda/Code/S3VersionKey LambdaCodeS3VersionKey10FC11BE: {"Type":"String","Description":"S3 key for asset version \"HelloCdkStack/Lambda/Code\""}
[+] Parameter Lambda/Code/ArtifactHash LambdaCodeArtifactHash305E64BB: {"Type":"String","Description":"Artifact hash for asset \"HelloCdkStack/Lambda/Code\""}

Resources
[+] AWS::IAM::Role Lambda/ServiceRole LambdaServiceRoleA8ED4D3B
[+] AWS::IAM::Policy Lambda/ServiceRole/DefaultPolicy LambdaServiceRoleDefaultPolicyDAE46E21
[+] AWS::Lambda::Function Lambda LambdaD247545B
[~] AWS::KMS::Key MyBucket/Key MyBucketKeyC17130CF
 └─ [~] KeyPolicy
     └─ [~] .Statement:
         └─ @@ -34,5 +34,24 @@
            [ ]       }
            [ ]     },
            [ ]     "Resource": "*"
            [+]   },
            [+]   {
            [+]     "Action": [
            [+]       "kms:Decrypt",
            [+]       "kms:DescribeKey",
            [+]       "kms:Encrypt",
            [+]       "kms:ReEncrypt*",
            [+]       "kms:GenerateDataKey*"
            [+]     ],
            [+]     "Effect": "Allow",
            [+]     "Principal": {
            [+]       "AWS": {
            [+]         "Fn::GetAtt": [
            [+]           "LambdaServiceRoleA8ED4D3B",
            [+]           "Arn"
            [+]         ]
            [+]       }
            [+]     },
            [+]     "Resource": "*"
            [ ]   }
            [ ] ]
```

Notice that not only was the CDK smart enough to grant lambda access to both the required `s3` and `kms` permissions for the particular bucket, it also updated the `KeyPolicy` on the KMS to grant access to the `LambdaServiceRole` principle.

Behold the generated YAML

```yaml
Resources:
  MyBucketKeyC17130CF:
    Type: AWS::KMS::Key
    Properties:
      KeyPolicy:
        Statement:
          - Action:
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
            Effect: Allow
            Principal:
              AWS:
                Fn::Join:
                  - ""
                  - - "arn:"
                    - Ref: AWS::Partition
                    - ":iam::"
                    - Ref: AWS::AccountId
                    - :root
            Resource: "*"
          - Action:
              - kms:Decrypt
              - kms:DescribeKey
              - kms:Encrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
            Effect: Allow
            Principal:
              AWS:
                Fn::GetAtt:
                  - LambdaServiceRoleA8ED4D3B
                  - Arn
            Resource: "*"
        Version: "2012-10-17"
      Description: Created by HelloCdkStack/MyBucket
    DeletionPolicy: Retain
    Metadata:
      aws:cdk:path: HelloCdkStack/MyBucket/Key/Resource
  MyBucketF68F3FF0:
    Type: AWS::S3::Bucket
    Properties:
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              KMSMasterKeyID:
                Fn::GetAtt:
                  - MyBucketKeyC17130CF
                  - Arn
              SSEAlgorithm: aws:kms
    DeletionPolicy: Retain
    Metadata:
      aws:cdk:path: HelloCdkStack/MyBucket/Resource
  LambdaServiceRoleA8ED4D3B:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Statement:
          - Action: sts:AssumeRole
            Effect: Allow
            Principal:
              Service:
                Fn::Join:
                  - ""
                  - - lambda.
                    - Ref: AWS::URLSuffix
        Version: "2012-10-17"
      ManagedPolicyArns:
        - Fn::Join:
            - ""
            - - "arn:"
              - Ref: AWS::Partition
              - :iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
    Metadata:
      aws:cdk:path: HelloCdkStack/Lambda/ServiceRole/Resource
  LambdaServiceRoleDefaultPolicyDAE46E21:
    Type: AWS::IAM::Policy
    Properties:
      PolicyDocument:
        Statement:
          - Action:
              - s3:GetObject*
              - s3:GetBucket*
              - s3:List*
              - s3:DeleteObject*
              - s3:PutObject*
              - s3:Abort*
            Effect: Allow
            Resource:
              - Fn::GetAtt:
                  - MyBucketF68F3FF0
                  - Arn
              - Fn::Join:
                  - ""
                  - - Fn::GetAtt:
                        - MyBucketF68F3FF0
                        - Arn
                    - /*
          - Action:
              - kms:Decrypt
              - kms:DescribeKey
              - kms:Encrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
            Effect: Allow
            Resource:
              Fn::GetAtt:
                - MyBucketKeyC17130CF
                - Arn
        Version: "2012-10-17"
      PolicyName: LambdaServiceRoleDefaultPolicyDAE46E21
      Roles:
        - Ref: LambdaServiceRoleA8ED4D3B
    Metadata:
      aws:cdk:path: HelloCdkStack/Lambda/ServiceRole/DefaultPolicy/Resource
  LambdaD247545B:
    Type: AWS::Lambda::Function
    Properties:
      Code:
        S3Bucket:
          Ref: LambdaCodeS3Bucket65766E44
        S3Key:
          Fn::Join:
            - ""
            - - Fn::Select:
                  - 0
                  - Fn::Split:
                      - "||"
                      - Ref: LambdaCodeS3VersionKey10FC11BE
              - Fn::Select:
                  - 1
                  - Fn::Split:
                      - "||"
                      - Ref: LambdaCodeS3VersionKey10FC11BE
      Handler: index.handler
      Role:
        Fn::GetAtt:
          - LambdaServiceRoleA8ED4D3B
          - Arn
      Runtime: nodejs8.10
      MemorySize: 576
      Timeout: 30
    DependsOn:
      - LambdaServiceRoleDefaultPolicyDAE46E21
      - LambdaServiceRoleA8ED4D3B
    Metadata:
      aws:cdk:path: HelloCdkStack/Lambda/Resource
      aws:asset:path: asset.7ecc77636deefb31954ea2e6aadf6d2c361a2943535a678d65f46a8de5e1f503
      aws:asset:property: Code
  CDKMetadata:
    Type: AWS::CDK::Metadata
    Properties:
      Modules: aws-cdk=0.33.0,@aws-cdk/assets=0.33.0,@aws-cdk/aws-cloudwatch=0.33.0,@aws-cdk/aws-ec2=0.33.0,@aws-cdk/aws-events=0.33.0,@aws-cdk/aws-iam=0.33.0,@aws-cdk/aws-kms=0.33.0,@aws-cdk/aws-lambda=0.33.0,@aws-cdk/aws-s3=0.33.0,@aws-cdk/aws-sqs=0.33.0,@aws-cdk/cdk=0.33.0,@aws-cdk/cx-api=0.33.0,@aws-cdk/region-info=0.33.0,jsii-runtime=node.js/v8.13.0
Parameters:
  LambdaCodeS3Bucket65766E44:
    Type: String
    Description: S3 bucket for asset "HelloCdkStack/Lambda/Code"
  LambdaCodeS3VersionKey10FC11BE:
    Type: String
    Description: S3 key for asset version "HelloCdkStack/Lambda/Code"
  LambdaCodeArtifactHash305E64BB:
    Type: String
    Description: Artifact hash for asset "HelloCdkStack/Lambda/Code"
```

Just for fun, if you compare the size of the CDK code vs the YAML, you end up with a 1:10 compression ratio (it would be much more if we used loops or conditionals). While this ratio isn't as good as say [gzip](https://superuser.com/questions/139253/what-is-the-maximum-compression-ratio-of-gzip), gzip also doesn't provision AWS infrastructure.


## Just Code

At the end of the day, the CDK is just code. Infrastructure as code. But actual code. Not some domain specific language. Not config. Code. This means that constructs like conditionals, loops, string interpolation and all the other things you are used to writing in code can be expressed naturally inside the CDK. No horrific CloudFormation macros, transformations or hacky workarounds.

Here is an example of conditionally changing auto scaling group settings depending on the stage
```typescript
  const asg = new autoscaling.AutoScalingGroup(scope, `ASG`, {
    vpc: vpc,
    minCapacity: 1,
    maxCapacity: (stage === 'gamma' ? 2 : 10),
    instanceType: new ec2.InstanceTypePair(ec2.InstanceClass.Burstable2,
      (stage === 'gamma' ? ec2.InstanceSize.Small: ec2.InstanceSize.Medium)
    ),
    machineImage: new ec2.AmazonLinuxImage(),
  });
```

Or what about programmatically adding stages to a pipeline through iteration?
```typescript
  let stages = ["gamma", "prod"]
  stages.forEach( stage => {
      let deploymentGroup = createDeploymentGroup({ scope: this, stage})
      const deployActionGamma = new codepipeline_actions.CodeDeployServerDeployAction({
          actionName: 'CodeDeploy',
          input: buildOutput,
          deploymentGroup,
      });
      pipeline.addStage({
          name: stage,
          actions: [deployActionGamma],
      });
  })
```

Or using string interpolation to pass in cloudformation resources to ec2 userdata?
```typescript
  const stage = gamma
  const asg = new autoscaling.AutoScalingGroup(scope, `ASG`, { ...asgProps })
  asg.addUserData(
    // install cw agent
    "wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm",
    "rpm -U ./amazon-cloudwatch-agent.rpm",
    `/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:AmazonCloudWatch-linux-agentconfig-${stage} -s`,
    `echo ${secretArn} > /home/ec2-user/dbSecretArn`,
    `echo ${lambda.functionName} > /home/ec2-user/canaryFunctionName`
  )
```

For those used to writing raw CloudFormation to provision infrastructure, coming from that world to the CDK feels like the same leap one makes when going from C++ to python. It is best captured in this [XKCD](https://xkcd.com/about/) comic.

![python image](https://www.explainxkcd.com/wiki/images/f/fd/python.png)

## Escape Valve

Now as great as the CDK is, it's not perfect and many features are still missing from the provided high level constructs. Thankfully, its easy to drop down to low level constructs where you have access to all CloudFormation properties.

If you don't want to deal with the hassle of managing a low level construct, you can also use the `addPropertyOverride` method to overwrite the generated CloudFormation values for existing high level constructs. For example, the `AccessControl` parameter is not supported in the S3 high level construct. One would add the following override to fix this.

```typescript
const bucket = new Bucket(this, 'MyBucket');
let cfnBucket = bucket.node.findChild('Resource') as CfnBucket
cfnBucket.addPropertyOverride("AccessControl", "LogDeliveryWrite")
```

Having access to low level constructs, property overwrites and the raw CloudFormation makes it easy to work around any missing features in the CDK and is the main reason I feel comfortable using it pre-GA release.



## Conclusion

Using the CDK has made me realize that the cake is not a lie and that it is possible to have all things all the time. In this case, all the things involves the expressivity of a programming language combined with the consistency of a config.

That being said, the CDK isn't all magic and rainbows. The library is in developer preview which means every version update will bring [breaking changes](https://github.com/awslabs/aws-cdk/blob/master/CHANGELOG.md). There is poor documentation for certain constructs, multi account support is [something to be desired](https://github.com/awslabs/aws-cdk/issues/49), and best practices around managing large CDK projects are yet to be established.

None of the drawbacks are deal breakers (in my opinion) and even if you use none of the higher level constructs - being able to generate CloudFormation using a programming language with type checking, auto completion, and incredible tooling is enough reason to give the CDK a try.

The CDK is my favorite shiny hammer of 2019 and I look forward to seeing the development of both the CDK and best practices around it. In the meanwhile, I've made up some of my own and will be posting it and other thoughts about the CDK in upcoming posts so stay tuned :)


