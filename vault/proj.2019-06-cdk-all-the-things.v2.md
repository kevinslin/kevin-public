---
id: OH5zmVOqqPno56zC
title: V2
desc: ''
updated: 1627421742784
created: 1627421709243
noindex: true
---

# AWS CDK: A Review

All of the Things
- How I learned to Keep Calm and Trust the CDK

- want the nuance of cloudformation, the expressivity of javascript, the type safety of java and the ability to create infrastructure 10x faster?

In that case, I like to introduce the AWS CDK. At the risk of sounding like a snake oil salesman, after using the CDK over the last several months, I admit that I'm in love.

The CDK is having the cake and eating it to and then being served a nine course meal with custom wine parings afterwards (because meals should always start off with cake). Okay, I'm done with the `admiration` now, let get into some specifics

## Overview

The AWS Cloud Development kit (CDK), simply put, is AWS's version of [Terraform](https://www.terraform.io/). The CDK allows users to use softare to define and provision infrastructure. The term infrastructure as code is a popular one heard in devops circles and typically has meant checking in your provisioning scripts or cloudformation templates into a github repo to manage. With CDK, your infrastructure is actually code. Specifically, it is [typescript](TODO) code, with support to other languages added using [jsii](https://github.com/awslabs/jsii). Currently supported languages include  C#/.NET, Java, JavaScript, Python, and TypeScript

Here's an example to create a lambda based cron job (the below snippet is taken from the [aws-cdk-examples](https://github.com/aws-samples/aws-cdk-examples/blob/master/typescript/lambda-cron/index.ts) repository

```
import events = require('@aws-cdk/aws-events');
import targets = require('@aws-cdk/aws-events-targets');
import lambda = require('@aws-cdk/aws-lambda');
import cdk = require('@aws-cdk/cdk');

import fs = require('fs');

export class LambdaCronStack extends cdk.Stack {
  constructor(app: cdk.App, id: string) {
    super(app, id);

    const lambdaFn = new lambda.Function(this, 'Singleton', {
      code: new lambda.InlineCode(fs.readFileSync('lambda-handler.py', { encoding: 'utf-8' })),
      handler: 'index.main',
      timeout: 300,
      runtime: lambda.Runtime.Python27,
    });

    // Run every day at 6PM UTC
    // See https://docs.aws.amazon.com/lambda/latest/dg/tutorial-scheduled-events-schedule-expressions.html
    const rule = new events.Rule(this, 'Rule', {
      scheduleExpression: 'cron(0 18 ? * MON-FRI *)',
    });

    rule.addTarget(new targets.LambdaFunction(lambdaFn));
  }
}

const app = new cdk.App();
new LambdaCronStack(app, 'LambdaCronExample');
app.run();

```

Something that you'll notice in the above example is that nowhere did you see YAML, JSON, or some custom config language. Its just code. The equivalent of the above in cloudformation:

TODO: generate cloudformation code

```

```

TODO: architecture

Traditionally, I've used raw cloudformation and SAM to provision infrastructure and coming from that world into the CDK feels like the same leap one makes when going from C++ to python

TODO: XKCD

## CDK Primer

To get started with CDK, download the `cdk` client and use it to generate your first app.

```
npm i -g aws-cdk
mkdir hello-cdk
cd hello-cdk
cdk init app --language=typescript (or --language=java, ...)
cdk deploy

```

## CDK Constructs

These are basic cloud components and can represent a single service (eg. S3 Bucket) or a set of services that represent a logical entity (eg. a Code Pipeline with four stages). Constructs are like modules in a programming language, which means you can create your own, create constructs that are the composition of multiple sub construcuts, use inheritance and create re-usable sharable constructs for common infrastructure.

The AWS CDK provides two types of constructs - Low and High Level.

High Level constructs will fill in most of the details for you and is akin to using the console on AWS where a lot of the nitty gritty with IAM permissions and dependent resources are created for you automagically.

- cloudtrail example #TODO
```

```

Low level constructs start with `Cfnxxx` prefix and there is a direct one-to-one mapping between a low level construct and the cloudformation properties of a particular service

- cloudtrail Cfn example #TODO
```

```

## Usage

I started using the CDK tentatively at first for some smaller client projects, building out ci/cd pipelines. I've actually been meaning to use it ever since it came out.

## Review

### Love

I don't use the word love lightly but here are some things I just abbsolutely love about the cdk.

#### fantastic tooling

Tooling is usually the thing that is saved for last in backend projects where the focus tends to be more about features then ux (eg. git). That's why I was delighted that I find the cdk great.

- `cdk init` will standup a cdk project skeleton to build out a new project
- `cdk diff` will create a DIFF of your current stack vs the one that you are currently deploying on
    - in case that last one skipped you by, the CDK will CREATE A DIFF OF NEW STACK CHANGES
    - apologies for the caps but this is a big stinkin deal because running cloudformation against existing production stacks in the past was always an ulcer inducing moment as you never knew if this was going to be the deploy that would accidentally replace your database
    - cloudformation introduced [changesets](https://aws.amazon.com/blogs/aws/new-change-sets-for-aws-cloudformation/) in 2016 so that you can do a diff but doing so was always painful

```bash
# in case there was a previous change set that was created but not deployed
aws cloudformation delete-change-set --stack-name $MY_STACK --change-set-name $MY_CHANGESET

# create changeset
# note that all parameters of a changeset have to be specified
# even if they are still using the same as the previous
aws cloudformation create-change-set --stack-name $MY_STACK --template-body "$(cat cloudformation.yaml)" --parameters ParameterKey=CrossAccountCondition,ParameterValue=true ParameterKey=ProjectName,ParameterValue=MyProject ParameterKey=S3Bucket,UsePreviousValue=true ParameterKey=CMKARN,UsePreviousValue=true ParameterKey=ProductionAccount,UsePreviousValue=true ParameterKey=TestAccount,UsePreviousValue=true --capabilities CAPABILITY_NAMED_IAM  --change-set-name $MY_CHANGESET

# finally get the diff, and this is in json form
aws cloudformation  describe-change-set --stack-name $MY_STACK --change-set-name $MY_CHANGESET

```
    - now look at the same in the CDK
TODO
```
cdk changeset

```
- `cdk synth` generates the raw cloudformation template
    - this is great just to verify that the code is creating the infrastructure that I want
    - provides an `eject` functionality so I can use native cloudformation if I don't want to use the cdk
- `cdk deploy` validates your cloudformation, creates a changeset and deploys it to your production stack
- `cdk ls` returns all your cloudformation stacks currently in your template

#### use a programming language

There's imense power from being able to use a programming language to create your infrastructure. This means you can use loops, conditionals, string interpolation, modules and all the other features that make programming languages great.

Some examples: TODO

-  autoscalin with conditionals and string interpolation
```typescript
  const asg = new autoscaling.AutoScalingGroup(scope, `ASG-${stage}`, {
    vpc: vpc,
    minCapacity: 1,
    desiredCapacity: 1,
    // keep max caapacity low for non-prod stage
    maxCapacity: (stage === 'gamma' ? 2 : 5),
    // use smaller instance sizes for non-prod stage
    instanceType: new ec2.InstanceTypePair(ec2.InstanceClass.Burstable2,
      (stage === 'gamma' ? ec2.InstanceSize.Small: ec2.InstanceSize.Medium)
    ),
    machineImage: new ec2.AmazonLinuxImage(),
  });
```

There generally is a tradeoff where you either use a config file like YAML, XML or JSON and declarative declare your resources to enable validation, drift detection and whatnot but it comes at the cost of your developers hating themsleves because writing and debugging YAML is something that no one wakes up excited to do.

Conversely, you have programming languages which are wonderfully expressive and can be tested and put through a whole integration pipeline but that very expressivity makes it hard to generate consistend builds of resources.

The CDK uses the expressivity of typescript to generate a configuration, cheating the tradeoff and developing both.

#### higher level constructs

AKA sane defaults. AKA how AWS is supposed to work if it weren't complicated.

Using the CDK is like using the console but in a good way. AWS suffers from a reproducibly problem because while it is generally straightforward to test a service using the console, it can be fiendishly hard to reproduce that in cloudformation and replicating that setup requires lots of tenacity, scouring the docs, checking your cloudtrail forums and having access to a local AWS expert. This is because AWS services usually require a whole bunch of dependent resources to get started, usually in the form of S3 buckets, IAM roles, Networking constructs, etc. Using the console, this is done for you atuomatically but it means that you're left scratching your head when tryinng to do the same in cloudformation.

A recent example - [cognito user pools](TODO: link) (not to be confused with [cognito federated identities](TODO: link)) is a managed service that lets customers implement both 1st and 3rd party authentication systems.

User pools have a [lambda triggers feature](TODO:link) which enable customers to create hooks for specific auth events (eg. run lambda to create an dynamodb entry when a customer first signs up). Using the console, just select the lambda and everything just works.

TODO: image
```

```

Use cloudformation, and you will get an access denied.

TODO: snapshot docs
```

```

Tursn out that you need to add a [resource based policy](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html) to the lambda so that cognito can invoke it.

When you use CDK and instantiate a user pool, any lambda that you associate with the pool will automatic have the permissions added.

- cdk code
```
TODO: user pool

```

- resulting cloudformation
```
TODO: cloudformation

```

#### typescript

Typescript has, and very much to my surprise, become my new favorite programming language. The feature is in the name - typescript does types and does them really well. When you use typescript with CDK, you get auto completion, documentation and runtime checks about your infrastructure. The importance of this cannot be overstated


#### 1st class cloudformation integration

This is where the CDK really shines because at the end of the day, if the library doesn't support a particular service, users can always drop down to cloudformation itself.

They can do this in two ways: either in the code by adding properties to a high level construct or by using low level constructs to begin with

- high level construct example, add acl to bucket
```
TODO

```

- low level construct example
```
TODO
```

Finally, customers can always `eject` by taking the generated cloudformation file and using it as is

### Edges

Like all software, the CDK isn't all magic and rainbows and there very definite pitfalls to take into consideration.

#### developer preview

AWS has the following disclaimer when you visit the [CDK developer guide](https://docs.aws.amazon.com/cdk/latest/guide/home.html)

```

This documentation is for the developer preview release (public beta) of the AWS Cloud Development Kit (AWS CDK). Releases might lack important features and might have future breaking changes.


```

Currently, what this means is that all releases come with breaking changes. As of this writing, we are on version `0.33` of the CDK. Over the past 3 weeks, the CDK changed from `0.31` to `0.32` and now to `0.33`. Each bump has come with breaking changes, usually in the form of renaming constructs (eg. `VpcNetwork -> Vpc`). The CDK [reference documentation](https://docs.aws.amazon.com/cdk/api/latest/versions.html) might not point to the latest version (it was completely unavailable for some time) and even then, a lot of the functionality of the library is mostly undocumented.

For the adventures, try figuring out what the following do: TODO

- eg. `cdk synth --no-staging`

#### poor documentation

some core constructs like `Token` are barely documented and can lead to frustrating debugging

TODO: token documentation
TODO: my github issue

#### cloudformation override

While it is possible to go into cloudformation, it would be nice if that weren't the case. But some common actions are still not possible

#### poor multi account support
- implementing multi account pipeline is poor

#### aws specific

Unlike other third party solutions, the CDK is an AWS specific cloudformation generation tool. While this means first party AWS support, there is zero support for any other clouds. Depending on what side of the cloud divide you're in, this can either be a non-issue or a deal breaker.

#### hard to deviate from public constructs

Having best practices setup is nice but might not make sense for everyone. For example, a fully redundant multi az rds cluster might not make sense if you are running a low traffic wordpress site or have a legacy single point of failure in a single AZ anyways.

For example, the default VPC construct with create  public and private subnets per Availability Zone with a NAT gateway per private subnet.

If you stick with this default in us-west-2, the CDK will construct 3 NAT gateways. The price of a NAT gateway is $0.045/h, regardless if you are sending traffic through it. That means that even if you don't send any traffic, this default VPC will run you ~$97/mo.

It's hard to change this in the high level construct, and there's an open issue here: https://github.com/awslabs/aws-cdk/issues/1305

Your only alternative is to use low level constructs but constructing a vpc, routing tables, subnets, is a painful undertaking and exactly the sort of work that the CDK is supposed to prevent.

### ux
#### programatic ids

CDK best practice is to let the CDK auto generate names for all your resources. This is necessary for certain changes that require a `REPLACE` operation where the CDK needs to tear down an old resource and stand up a new one but results in all your

#### nested stacks

CDK doesn't support nested stacks. You can cheat by using the cloudformation construct but at that point, you're in the same circle in hell as the people that use metaprogramming in java. This is okay per say because you're cdk uses different paradigm but you might run into issues if you're cloudformation gets too bib, especially if you are doing multi accounts.

## Use Cases
- use cdk for all the places you would have used terraform/sam/serverless/troposphere/etc
- create re-usable, tested, common components
- create dashboards (possible in AWS but cdk makes it easier)

- create email subscriptions to sns programatically

## CDK vs *

The CDK comes to the infrastructure provisioning in a crowded market. There's a saying at AWS - if something is worth doing, its worth doing (at least) twice. If you look at AWS's own efforts around declarative infrastructure provisioning on AWS, you'll first party services such as `cloudformation`, `Amplify` and the `Serverless Application Model`.

The problem with all these solutions is because there are too complicated (eg. cloudformation) or too limited (eg. SAM). My evaluation of IaaS software falls under the following criteria:

- easy to do the easy things
- easy to do the hard things

There are numerous third party providers, a few listedf below:
- terraform
- [troposphere](https://github.com/cloudtools/troposphere): like CDK but community supported and written in python
- [pulumi](https://pulumi.io/)
- amplify
- sam
- serverless
- etc.


## Conclusion

The AWS CDK is the next iteration in the steady trend of infrastructure as code. In that regards, it actually delivers and makes it possible to write, provision, and test infrastructure, as code.

Yes, there are still lots of edges to be smoothed out and it is still in developer preview (aka things could break all the time) but the value that it delivers far outweights any of the setbacks.

Using the CDK has made me realize that the cake is not a lie and that it is possible to have all things all the time - in this case its the expressivity of a programming language combined with the consistency and declarativity of a complicated configuration. The CDK is my favorite new hammer of 2019 and I look forward to seeing the development of both the CDK and best practices around it. In the meanwhile, I've made up some of my own and will be posting it and other updates about the CDK in upcoming posts so stay tuned :)


--- 
# Other
Currently it is not very exciting and
```
Resources:
  CDKMetadata:
    Type: AWS::CDK::Metadata
    Properties:
      Modules: aws-cdk=0.33.0,@aws-cdk/cdk=0.33.0,@aws-cdk/cx-api=0.33.0,jsii-runtime=node.js/v8.13.0
```

Saying the CDK provisions AWS infrastructure is like saying a rocketship can fly - it fails to do justice to something that is out of this world. With the CDK, you can initialize AWS services that fill in missing values with sane defaults and also use AWS best practices to do so. High Level constructs will fill in most of the details for you and is akin to using the console on AWS where a lot of the nitty gritty with IAM permissions and dependent resources are created for you automagically.

And because the CDK generates cloudformation, I can always verify what resources get created and unlike other frameworks that try to simplify cloudformation development(cough* SAM), I get to use the full power of cloudformation.

