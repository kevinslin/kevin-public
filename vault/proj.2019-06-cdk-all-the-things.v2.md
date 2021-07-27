---
id: by9Ix8ExUcaF6JwA
title: V2
desc: ''
updated: 1627421797733
created: 1627421774417
noindex: true
---

# CDK All the Things

sub: An whirlwind guide of all the things you can do with the AWS Cloud Development Kit

Have you ever thought to yourself - gee, sure would be nice to provision AWS infrastructure with the expressivity of (es8) javascript, the type safety of java and the declarative nuance of native cloudformation?

Well think no more because the AWS Cloud Development Kit (CDK) is here. First [announced](https://aws.amazon.com/blogs/developer/aws-cdk-developer-preview/) in August of 2018, the CDK, simply put, is AWS's version of [Terraform](https://www.terraform.io/). The CDK enables customers to use code to define and provision infrastructure (also known as [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code)) (IaC). Whereas many IaC solutions take the form of config files (eg. cloudformation) or a custom domain specific language (eg. terraform), the CDK allows you to use native code. The CDK itself is written in [typescript](https://www.typescriptlang.org/) and uses the [jsii](https://github.com/awslabs/jsii) to create clients in other languages. Currently supported languages include  C#/.NET, Java, JavaScript, Python, and TypeScript

I was skeptical at first. IaC on AWS was a crowded space and between 3rd party providers (terraform, troposphere, pulumi, etc) as well as AWS native alternatives (cloudformation, SAM, Amplify), I wondered what utility yet another IaC could do. But I wanted to give it a shot and what started off as a weekend of prototyping toy projects with the CDK turned into half a year of building extensive AWS architectures in production. Yes in production. Using a framework that is in developer preview with no official GA release date in sight. By the end of this article, I hope to convince you that this is not madness and why you too might want to consider using the CDK for your next project.

## Initial Setup

To start creating infrastructure via the CDK, download the cdk cli which you can use to generate a skeleton app.

```bash
npm i -g aws-cdk
mkdir hello-cdk
cd hello-cdk
cdk init app --language=typescript (or --language=java, ...)
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

The action happens in `hello-cdk-stack.ts`. A `cdk.Stack` represents a cloudformation stack and you start building your stack by creating `constructs` (explained in the next section) inside the stack.

```
import cdk = require('@aws-cdk/cdk');

export class HelloCdkStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
  }
}
```

## Constructs

Constructs are basic cloud components and can represent a single service (eg. S3 Bucket) or a set of services that represent a logical entity (eg. a Code Pipeline with four stages). There is a construct for every AWS service that cloudformation supports (which shockingly is still not every service - looking at you Amazon Connect...).

The CDK provides two types of construct: Low and High Level.

High level constructs let you get started quickly and will use sane default values for all unfufilled parameters. For example, take the VPC construct

```typescript
import ec2 = require('@aws-cdk/aws-ec2');

const vpc = new ec2.Vpc(this, 'VPC');
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

If you would like to have further control, you can drop down to low level constructs. Low level constructs are prefixed with `Cfn` and there is a direct one to one mapping between all the properties of a low level construct and the resulting cloudformation.

```typescript
const vpc = new ec2.CfnVPC(this, 'Resource', {
  cidrBlock: "10.0.0.0/16"
  enableDnsHostnames: true,
  enableDnsSupport: true,
  instanceTenancy: 'default'
});
```

This creates just a vbc and leaves it up to you to manually define subnets, routing tables and everything else. Low level constructs allow customers to implement features that are not yet supported in high level constructs, and you get the nice bonus of typechecking of properties by creating them with the CDK. That being said, most of this article will focus on high level constructs.

## Provisioning a Service

To start using the CDK, you need to install the constructs that you want to use. Every service is inside a separate package, with the following schema `@aws-cdk/aws-{service_name}`

For example, to create a s3 bucket, add the `@aws-cdk/aws-s3` to the project.

```bash
yarn add @aws-cdk/aws-s3
```

Next, add the bucket into the `hello-cdk-stack.ts` file
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

# alternatively, run `tsc --watch` to have typescript watch the project and re-compile on every change
```

Now run `cdk diff` to see what's changed
```
Resources
[+] AWS::S3::Bucket MyBucket MyBucketF68F3FF0
```

Run `cdk synthesize` to generate the cloudformation

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

Run `cdk deploy` to create a cloudformation deployment
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
Now lets say you were using the bucket to store sensitive information and wanted to encrypt your bucket using a custom kms key. Simply add the following line...

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

I cannot overstate how amazing it is to get a diff of your cloudformation changes. This alone could have justified usage of the CDK. Now some people at this point might bring up [cloudformation changesets](https://aws.amazon.com/blogs/aws/new-change-sets-for-aws-cloudformation/). This is the cloudformation native way of getting a diff - the equivalent of a `dry-run` option or a `terraform plan`.  To get a diff via a changesets, one would run the following commands.

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


As you see, figuring out the `diff` by changeset required far more scripting and was not as high fidelity as `cdk diff`. Whereas the changeset only highlighted the creation of the CMK key and bucket encryption, `cdk diff` actually prints out the key and bucket policies. Something else that's not obvious in the above script is that create-change-set is asynchronous and if you wanted to put it into a script, you would actually need to check that the change set had been created before being able to run `describe-change-set`. Finally, we are currently working with a very simple template, if the template had parameters, a changeset would require that every parameter be specified (even if paramter values haven't changed).


So short story - after using `cdk diff`, I can never be satisfied with changesets again.

Okay, now that I've finished raning about changesets, we can proceed to deploying changes by running `cdk deploy`. The CDK will flag changes that involve IAM and ask you for confirmation before proceeding.

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
So now we've seen a simple example of creating a bucket and changing a property. Where the CDK truly shines is when you use it to provision multiple services that need to talk to each other.  Lets say that you want to use a lambda to do some processing of data after an object is created in the bucket. Normally, to have a lambda access an encrypted S3 bukcet, you would have to do the following:
1. create a lambda and provision it via console/SAM/serverless/etc
2. create an execution role for the lambda that is assumable by the lambda service
3. attach a policy to aforementioned execution role that lets lambda write to cloudwatch logs
4. attach a policy to aforementioned execution role that lets lambda perform the necessary `s3` operations scoped to the particualr bucket
5. attach a policy to aforementioned execution role that lets lambda perform the necessary `kms` operations scoped to the particualr bucket
6. update the kms policy to allow access for the lambda principle

There's a lot involved here, and even if you use the console, steps 4-6 would still be operations you would have to perform manually. Lets see what this looks like when using the cdk.

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

// this will add necessary permissions to the lambda service role AND the kms key policy
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

This creates the following cloudformation.

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

Just for fun, if you count the number of characters used for the cdk code vs the YAML, you end up with a 1:10 difference. While this might not be [gzip](https://superuser.com/questions/139253/what-is-the-maximum-compression-ratio-of-gzip), gzip also doesn't provision AWS infrastructure.

## Escape Valve

Now as great as the CDK is, its not perfect. It's actually still in developer preview. There are features that are missing in high level constructs  but they are easily fixed by dropping down to low level constructs. If you don't want to deal with the hassle of managing a low level construct, you can also use the `addPropertyOverride` method to overwrite generated cloudformation values for existing constructs. For example, the `AccessControl` parameter is not supported in the s3 high level construct but is fixed with the following two lines.

```typescript
const bucket = new Bucket(this, 'MyBucket');
let cfnBucket = bucket.node.findChild('Resource') as CfnBucket // every high level construct consists of low level constructs, we need to do this funky conversion if we want to override the raw cloudformation
cfnBucket.addPropertyOverride("AccessControl", "LogDeliveryWrite")

```

Having access to low level constructs, property overwrites and the resulting cloudformation make it possible to start using it even before the GA release, because at worst, you can resort to the tools that you already know how to use.


## Just Code

At the end of the day, the CDK is just code. Infrastructure as code. But actual code. Not some domain specific language. Not config. Code. This means that constructs like conditionals, loops, string interpoloation and all the other things you are used to writing in code can be expressed naturally inside the CDK. No horrific cloudformation macros, transformations or hacky workarounds.

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

Here is an example of adding stages to a pipeline by iterating through an array
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

Here is an example of using string interpolation to bootstrap the user data of ec2 instances
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

For those used to writing raw cloudformation to provision infrastructure, coming from that world to the CDK feels like the same leap one makes when going from C++ to python.

![python image](https://www.explainxkcd.com/wiki/images/f/fd/python.png)


## Conclusion

Using the CDK has made me realize that the cake is not a lie and that it is possible to have all things all the time - in this case its the expressivity of a programming language combined with the consistency and declarativity of a complicated configuration.

That being said, the CDK isn't all magic and rainbows and there very definite pitfalls to take into consideration. The library is in developer preview which means every version update will bring [breaking changes](https://github.com/awslabs/aws-cdk/blob/master/CHANGELOG.md). Usually these are minor and involve objects being renamed (eg. `VpcNetwork -> Vpc`) but these are easily fixed. There is poor documentation for a lot of constructs, multi account support is [something to be desired](https://github.com/awslabs/aws-cdk/issues/49), and not all services are equally developed . But because the CDK is just a superset of CloudFormation and all cloudformation of all services are accessible as low level constructs, it means that none of these are deal breakers because at workst, you have an object oriented, type checked, auto completable means of generating cloudformation.

The CDK is my favorite new hammer of 2019 and I look forward to seeing the development of both the CDK and best practices around it. In the meanwhile, I've made up some of my own and will be posting it and other thoughts about the CDK in upcoming posts so stay tuned :)



