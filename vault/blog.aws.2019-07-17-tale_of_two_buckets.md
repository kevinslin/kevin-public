---
id: f2542b0c-5cbd-49b9-84d8-151ccab99dea
title: A Tale of Two Buckets
desc: ''
updated: 1627078981354
created: 1596416622316
tags:
  - coe
  - hipaa
  - devops
  - s3
  - cloudfront
  - athena
  - iam
  - cloudwatch
  - governance
  - scp
  - lambda@edge
  - aws organization
layout: single
date: '2019-07-17'
categories: aws
category: aws
toc: true
header:
  teaser: /assets/images/20190724-tale_of_two_buckets/buckets.jpg
excerpt: >-
  The story of a bad multi-account deployment and the ensuing investigation to
  find the truth. A tale of intrigue, identity and access.
---

!["Multi-Account S3 Buckets Representation"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/buckets.jpg)


You could be excused if you were confused from reading the title. AWS is many things but simple is not the first (or second) way most would describe it (even for services that have *"simple"* in the name). This is especially true when you throw multiple AWS accounts into the mix. In the past, doing anything multi account on AWS involved doubling your setup time and hoping that there wouldn't be some IAM quirk or service limit that would force you to re-architect. This has gotten much better over time, and services like [AWS Organizations](https://aws.amazon.com/organizations/) and [AWS Control Tower](https://aws.amazon.com/controltower/) paired with features such as [Cloudformation Stack Sets](https://aws.amazon.com/about-aws/whats-new/2017/07/aws-cloudformation-supports-multiple-account-and-region-provisioning-with-stackset/) and [CloudTrail support for organizations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html) have made it drastically easier to do multi-account buildouts. That being said, there are many edges left that can still draw blood.

This article is a deep dive into an deployment issue caused by yours truly after running head on into such an edge.

## Overview

Our story begins with a [consulting client](https://thence.io)  - we will refer to them as HealthCorp for the rest of the article. HealthCorp is a medical startup based in the states. They are building a webapp which, among other things, will be used to collect Patient Health Information (PHI). HealthCorp requires that their AWS buildout be [HIPAA](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) compliant. HIPAA, otherwise known as the "Health Insurance Portability and Accountability Act of 1996", is legislation which enumerates security measures and policies that need to be put in place when working with PHI. While it has the laudable goal of protecting patient information, actually satisfying the many, often vaguely defined, requirements can be an arduous task and can greatly slow development time.

While it's not listed anywhere in HIPAA that you need a multi-account AWS setup for your service, requirements like [least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) coupled with the inherent complexity of [IAM](https://start.jcolemorrison.com/aws-iam-policies-in-a-nutshell/) means that going multi-account is a practical necessity. This can be a good thing because it can actually result in greater agility for the organization. With multiple accounts, we can completely lock down `prod` accounts which contains PHI, simplifying compliance,  and grant developers full access to `dev` accounts which do not, increasing velocity. This was one of my initial recommendations for HealthCorp and led to the following multi-account pipeline for HealthCorp's client facing webapp.

![Code Pipeline with CodeBuild, Lambda, S3 and CloudFront](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/pipeline.png)

We setup a pipeline in a central `tools` account using [CodePipeline](https://aws.amazon.com/codepipeline/), an AWS service that makes it easy to do CI/CD on AWS. CodePipeline consists of stages and actions. A stage consists of one or more actions. Depending on the type of action, an action can take an input artifact and/or export an output artifact. Artifacts are uploaded to an artifact bucket which is configured when first creating the pipeline. For more information, you can read the [official docs](https://docs.aws.amazon.com/codepipeline/latest/userguide/concepts.html).

The HealthCorp pipeline consists of 5 stages: `Source`, `Build`, `Dev`, `Approval`, `Prod`.  It watches for changes from a private Github repo (Source stage) and then builds changes using [CodeBuild](https://aws.amazon.com/codebuild/) (Build stage). Codebuild compiles the changes and then uploads them to a central artifact bucket. The pipeline then deploys changes via a Lambda that has permissions to read from the artifact bucket and deploy to the buckets in the `dev` account (Dev stage). If integration tests pass, a notification is sent to relevant parties who can choose to approve the changes (Approval stage). If so, another lambda will download the artifacts from the build stage and deploy them to the `prod` account (Prod stage).

 Within each service account, we use [CloudFront](https://aws.amazon.com/cloudfront/) in front of a S3 bucket to securely deliver content to customers at low latencies. As part of the deployment, Lambda will perform a CloudFront [cache invalidation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html) on changed content.

A short while after setting up the pipeline, my client requested we block public access to the site. Since the webapp was still under development, they didn't want an incomplete version of the final product to be accessible to the public. I proposed using [basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) via [lambda@edge](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-edge-add-triggers.html) since it would do the job and also be quick to implement. They agreed and so we put the solution in place.

One common gotcha with the approach is that restricting access to CloudFront did not necessarily restrict access to S3 - if the objects were uploaded with public read ACLs (which was the case here), then customers/crawlers/bots/spies could bypass CloudFront and still access the S3 bucket directly.

![User bypassing lambda@edge and accessing S3 bucket directly](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/auth_bypass.png)

In order to lock down S3, we would need to lock down the S3 bucket to deny access to all except CloudFront.  This meant doing the following steps:

- create a [Origin Access Identity](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html) (OAI) for CloudFront and associate this OAI with the S3 bucket origin
- update the bucket policy to allow the aforementioned OAI access to the bucket
- update the S3 bucket to [block public access](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-block-public-access.html)

After executing each of the aforementioned steps and confirming the changes in `dev`, we pushed changes to `prod` and called it a day.

## Trouble

The next day, I got a ping from my client. They were unable to access the HealthCorp webapp. I fired up my browser and was able to reproduce the issue by navigating to the home page.

![S3 Bucket access denied](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/s3_403.jpg)

Talking it over with my client, we agreed to rollback the changes in `dev` to unblock the devs. We did this by turning off the `block public access` controls on the bucket which meant objects within were publicly readable by both CloudFront (good) and the rest of the world (bad). Since the bucket was originally public and only contained prototype landing page components, this didn't count among the [worst S3 security leaks](https://businessinsights.bitdefender.com/worst-amazon-breaches) of all time. That being said, this was still a bad situation and one I wanted to remediate as soon as possible.


## Investigation

First order of business, how did we miss this? I remembered checking the site the previous day after blocking public access and doing a full CloudFront [cache invalidation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html). One possible explanation was that the browser cache was not disabled and that I was looking at a cached version of the site.

![Chrome Cache](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/chrome_cache.jpg)
<small>Disabling cache in Chrome</small>

To confirm, I re-enabled `block public access`, invalidated CloudFront, disabled the browser cache, and was able to trigger an immediate 403. This was a good first step because it meant that the change to lock down the S3 bucket was most likely the cause of the current issue.

To investigate the root cause, I then made a prioritized list of services to investigate for clues:

1. S3: misconfiguration with bucket permissions
2. CloudFront: misconfiguration with the OAI
3. Lambda@Edge: lambda was mangling the request
4. AWS Organizations: [SCP policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) were denying the request

### S3

![S3 Bucket](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/s3.png)

S3 is one of the oldest AWS services, used in everything from serving static websites to storing satellite data (and even as a [file system](https://github.com/s3fs-fuse/s3fs-fuse)). The security model had to evolve to handle anything that the internet could throw at it and as a consequence, it has the most permission mechanisms of any AWS service.

![S3 Permissions](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/s3_permissions.jpg)
<small> S3 permission mechanisms </small>

1. [Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-block-public-access.html), as the name implies, denies public access to a bucket by both preventing new ACLs and Bucket Policies that grant public access as well as override existing ACL's and Bucket Policies that grant public access. This was announced in [late 2018](https://aws.amazon.com/blogs/aws/amazon-s3-block-public-access-another-layer-of-protection-for-your-accounts-and-buckets/) and enabled in all new S3 buckets by default. In our case, this was initially disabled to allow uploading public objects but then enabled to block public access.
3. [Access Control List](https://docs.aws.amazon.com/AmazonS3/latest/dev/S3_ACLs_UsingACLs.html) control read/write access on buckets and their objects. Access can be granted to AWS accounts, the public, and an S3 log delivery group. This was left at the default which meant only the account owner had read/write access.
4. [Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-block-public-access.html) enable advanced access control policies on buckets. They are like IAM policies but associated to buckets. By default, this is blank. We had the following policy attached to enable CloudFront read access.
    ```json
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity ******"
        },
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::********/*"
    }
    ```
5. [ CORS Configuration ](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html) restricts S3 access to allowed origins. This was left as the default (no CORS based restrictions).

Since checking all bucket level permissions didn't reveal any issues, I also scanned the [S3 access logs](https://docs.aws.amazon.com/AmazonS3/latest/dev/ServerLogs.html) using [Athena](https://aws.amazon.com/blogs/big-data/analyzing-data-in-s3-using-amazon-athena/) (AWS managed [Presto](https://aws.amazon.com/big-data/what-is-presto/)). S3 access logs provided detailed records of each request made to a bucket and recorded metadata like user-agent, error codes and http status.

I compared the S3 access logs of the webapp vs the access logs of [my podcast](https://folkstories.org) which is also hosted via S3 and served from CloudFront using the same setup

- access log from HealthCorp
```
"bucketowner" , "bucket" , "requestdatetime"          , "remoteip" , "requester" , "requestid" , "operation"     , "key"      , "requesturi_operation" , "requesturi_key" , "requesturi_httpprotoversion" , "httpstatus" , "errorcode"  , "bytessent" , "objectsize" , "totaltime" , "turnaroundtime" , "referrer" , "useragent"         , "versionid" , "hostid" , "sigv" , "ciphersuite"               , "authtype" , "endpoint"                          , "tlsversion"
***           , ***      , 30/Jun/2019:16:50:35 +0000 , ***        , *******     , ******      , REST.GET.OBJECT , index.html , GET                    , /index.html      , HTTP/1.1                      , 403          , AccessDenied , 243         ,              , 18          , -                , "-"        , "Amazon CloudFront" , -           , ****     , SigV4  , ECDHE-RSA-AES128-GCM-SHA256 , AuthHeader , ********.s3.us-west-2.amazonaws.com , TLSv1.2
...
```

- access log from my podcast
```
"bucketowner" , "bucket"   , "requestdatetime"            , "remoteip" , "requester" , "requestid" , "operation"       , "key"              , "requesturi_operation" , "requesturi_key"    , "requesturi_httpprotoversion" , "httpstatus" , "errorcode" , "bytessent" , "objectsize" , "totaltime" , "turnaroundtime" , "referrer" , "useragent"             , "versionid" , "hostid" , "sigv"  , "ciphersuite"                 , "authtype"   , "endpoint"              , "tlsversion"
"*****"       , "********" , "30/Jun/2019:16:53:54 +0000" , "***"      , "***"       , ***         , "REST.GET.OBJECT" , "podcasts/***.mp3" , "GET"                  , "/podcasts/***.mp3" , "HTTP/1.1"                    , "206"        , "-"         , "28223100"  , "28223100"   , "284"       , "80"             , """-"""    , """Amazon CloudFront""" , "-"         , "****"   , "SigV4" , "ECDHE-RSA-AES128-GCM-SHA256" , "AuthHeader" , "****.s3.amazonaws.com" , "TLSv1.2"
...
```

If you look closely, you'll find the only meaningful difference between the two access logs is that the one from HealthCorp returned a 403 and the one from my podcast returned a 206 (and we already knew this from the current state of the world).

### CloudFront

![CloudFront](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/cloudfront.png)

An area where I'd seen issues with CloudFront in this sort of setup is misconfiguration with the OAI. An OAI is a special CloudFront user that can be associated with S3 origins to restrict access.

As a sanity test, I checked whether the same OAI that was given permission to the S3 Bucket was actually associated with our S3 origin. In order to do this, I compared the OAI ID with the ID in the S3 bucket policy.

- CloudFront origin id

```bash
aws cloudfront list-cloud-front-origin-access-identities

{
    "CloudFrontOriginAccessIdentityList": {
        "Items": [
            {
                "Id": "***",
                "S3CanonicalUserId": "###",
                "Comment": "cloudfront identity"
            }
        ]
    }
}
```

- S3 bucket policy

```json
{
    "Effect": "Allow",
    "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity ***
    },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::********/*"
}
```

The `***` from CloudFrontOrigin Id needed to match the `***` from S3 Bucket Principal (which it did).

The next step of the sanity test was making sure the OAI was associated to the correct S3 origin (CloudFront lets you specify multiple OAIs and origins and mix and match OAIs with origins)

```bash
aws cloudfront get-distribution --id $dist_id
{
    "ETag": "***",
    "Distribution": {
        "Id": "***",
        ...,
        "DistributionConfig": {
            ...
            "Origins": {
                "Quantity": 1,
                "Items": [
                    {
                        ...
                        "S3OriginConfig": {
                            "OriginAccessIdentity": "origin-access-identity/cloudfront/***"
                        }
                    }
                ]
            }
        }
    }
}
```
The `***`  from `S3OriginConfig` should match the CloudFrontOrigin Id from earlier (which it did).

Since there was nothing I could find wrong with the OAI setup, I also used Athena to browse the [CloudFront logs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) but this too didn't reveal anything new besides the fact that we were getting a 403 denied.

```
date      	 time     	 location 	 bytes 	 request_ip   	 method 	 host               	 uri          	 status 	 referrer         	 user_agent  	 query_string 	 cookie 	 result_type             	 request_id                                               	 host_header 	 request_protocol 	 request_bytes 	 time_taken 	 xforwarded_for 	 ssl_protocol 	 ssl_cipher                  	 response_result_type    	 http_version 	 fle_status 	 fle_encrypted_fields
6/30/2019 	 17:31:32 	 HIO51-C1 	 581   	 *** 	 GET    	 ***.cloudfront.net 	 /            	 403    	 -                	 Mozilla/5.0 	 -            	 -      	 Error                   	 dVdkNuK6kyFAu6mS12YJj41s2Uahg4UC9AtAEGlfKIPceqdhrDB2gQ== 	 ***.com     	 https            	 39            	 0.076      	 -              	 TLSv1.2      	 ECDHE-RSA-AES128-GCM-SHA256 	 Error                   	 HTTP/2.0     	 -
6/30/2019 	 17:31:32 	 HIO51-C1 	 582   	 *** 	 GET    	 ***.cloudfront.net 	 /favicon.ico 	 403    	 https://***.com/ 	 Mozilla/5.0 	 -            	 -      	 Error                   	 xHER0TBGRmfmmLerNw2aPfM9lCDNUJkcfuyGwp5XjYbyRuUf5ht3BA== 	 ***.com     	 https            	 86            	 0.079      	 -              	 TLSv1.2      	 ECDHE-RSA-AES128-GCM-SHA256 	 Error                   	 HTTP/2.0     	 -
```


### Lambda@Edge

![Lambda](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/lambda.png)

Lambda@Edge is a Lambda that is triggered when a user sends a request to a CloudFront distribution. Since it can modify the request, errors here can lead to the request being invalid. Lambda@Edge can be triggered at the following points in a request lifecycle:

- When CloudFront receives a request from a viewer (viewer request)
- Before CloudFront forwards a request to the origin (origin request)
- When CloudFront receives a response from the origin (origin response)
- Before CloudFront returns the response to the viewer (viewer response)

For our use case of doing basic auth, we used `Viewer Request` which means that the Lambda wasn't even involved in forwarding the request to the origin. Since we saw the S3 access logs show requests from CloudFront, it meant that authentication via Lambda was successful and scanning Lambda metrics/logs revealed a similar story. So the issue wasn't here either.

### AWS Organizations

![AWS Organization](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/organization.png)

AWS Organizations is used for the central management, billing and governance of multiple AWS accounts. For governance, Organizations uses [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) (SCP) to restrict access in member accounts. SCPs are like IAM rules but apply to AWS Accounts and can only `Deny` access. That means that you can have a rule like the following which would deny accounts the ability to stop cloudtrail logging, even if they had `AdminFullAccess` within the account.

```json
{
    "Effect": "Deny",
    "Action": "cloudtrail:StopLogging",
    "Resource": "*",
}
```

This is great for governance but means that debugging IAM issues can be tricky as it's not always obvious if a SCP policy is denying access or whether you just misconfigured an IAM rule.

SCPs become really useful when combined with IAM conditionals that enforce IAM policies only when certain conditions are met. This supports crafting policies like the following which deny AWS access to all except IAM roles if not authenticated via MFA.

```json
{
    "Effect": "Deny",
    "NotAction": [
    "iam:CreateVirtualMFADevice",
    "iam:EnableMFADevice",
    "iam:GetUser",
    "iam:ListMFADevices",
    "iam:ListVirtualMFADevices",
    "iam:ResyncMFADevice",
    "sts:GetSessionToken"
    ],
    "Resource": "*",
    "Condition": {
        "BoolIfExists": {
            "aws:MultiFactorAuthPresent": "false"
        },
        "ForAnyValue:StringNotLike": {
            "aws:PrincipalARN":[
            "arn:aws:iam::*:role/*",
            ]
        }
    }
}
```

The above is not just an example but an actual policy we have in place at HealthCorp. We have other policies that denied the ability to change config and cloudtrail settings but this is the only one that could be related to our issue. Since the above policy denies access to almost all AWS actions if not authenticated via MFA, could it be affecting the OAI which was accessing S3 without MFA? We put in an exception for IAM roles but is an OAI a role, a user or something else entirely? The AWS documentation about [OAIs](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_S3OriginConfig.html#cloudfront-Type-S3OriginConfig-OriginAccessIdentity) do not shed further light on this.

To test if this was the case, I created the same SCP policy inside a different account with a working CloudFront distribution and S3 setup and verified that enforcing the SCP policies did not interfere with traffic which meant this wasn't it either.

## Problem

At this point, we went through our whole lineup of potential culprits and still did not come up with a root cause.  It was here that I had a gnawing feeling that I was missing something obvious.

A great engineer that I knew at AWS once said:

> When you see hoof prints, think horses not zebras.

![Horse](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190724-tale_of_two_buckets/conv-horse.jpg)

This referred to this idea that when you are not sure whats wrong but see evidence that could imply multiple failure modes, go for the most obvious one.

Me investigating Cloudfront Origin Identities and whether AWS Organization SCP policies were somehow blocking access to them due to a lack of MFA had left horses and zebras altogether and was entering unicorn territory. I needed to reset and look for horses.

What did we know?
- there's a 403 error when CloudFront tries to access S3
- CloudFront is configured correctly
- there are no interfering SCPs

All signs point to it being an issue with S3 and not having access. Therefore, lets enumerate all the ways we can get an access denied on S3.
- the four bucket level access restrictions on S3 which we mentioned earlier
    - Block Public Access
    - Access Control List
    - Bucket Policy
    - CORS Configuration
- additional possible S3 access restrictions
    - IAM Policies of the AWS user/role/group that deny access to S3
    - SCP Policies which can deny any IAM based access of any AWS resource within an account
    - [S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock.html) which is a policy that can only be set in place during bucket creation and restricts when objects can be deleted (either for a fixed amount of time or indefinitely)
    - [KMS Key]() which can be used to encrypt bucket contents and can only be decrypted if BOTH the Key Policy and either the S3 resource policy or the IAM principle policy have [the necessary permissions](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html)

The aforementioned policies all affect S3 at the bucket level and have at this point all been ruled out (and neither **KMS** nor **S3 Object Lock** apply in this case).

But what about permissions that weren't at the bucket level? S3 had one more access model that I failed to check - S3 Object ACLs.  Object ACLs do exactly what the name implies - they control read/write access on a per object level.

To check the object ACL, I ran the following to get the object ACL of the index page.

```bash
aws s3api get-object-acl --bucket *** --key index.html

An error occurred (AccessDenied) when calling the GetObjectAcl operation: Access Denied
```

Bingo, this was my answer. My IAM user had `AdminFullAccess` credentials but was still getting an access denied. I now knew what was causing the issue.

If you will remember from the beginning of the article, the way objects are deployed to this S3 bucket was through a lambda that would upload them from a different AWS account.

And now a AWS Certification question for the reader: if you upload objects using a role in Account A to a bucket in account B, who is the owner of that object?

Is it
- a) Account A?
- b) Account B?
- c) Account A & B
- d) None of the above. We don't believe in ownership and have invested all our assets in a decentralized cryptocurrency

## Resolution

The answer, is a). This is actually one of the most common gotchas when using cross account roles to upload S3 objects - if access isn't explicitly granted to the target bucket account owner for the given object, that owner will be unable to read or modify said object.

In our case, we were initially able to read objects but only because there were uploaded with the `public-read` ACL. Once public access was blocked, so was all access to said objects. Had we done everything inside a single AWS account, no additional permissions would have been required but because the objects belonged to an external account, explicit access was required.

When an entity in a different account uploads an object via a cross account role to a target account, object ACLs are the only means in which to grant entities in the target account access to said objects. The way to specify entities within the object ACL framework is through canonical ids which are obfuscated AWS account identities. You can find your AWS account identity by following the instructions [here](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html#FindingCanonicalId).

The problem in our case was that it wasn't enough to grant the `service` account full access to the objects because it was the OAI that was accessing these objects and therefore the OAI that needed to be granted access.

Fortunately, OAI has ~~a hack~~ a [S3CanonicalUserId](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_GetCloudFrontOriginAccessIdentity.html) property which you can (only) find via an API call like the one below:

```bash
aws cloudfront list-cloud-front-origin-access-identities
{
    "CloudFrontOriginAccessIdentityList": {
        "Items": [
            {
                "Id": "****",
                "S3CanonicalUserId": "*******",
                "Comment": "cloudfront identity"
            }
        ]
    }
}
```
In order to grant access, we needed to add the following lines to the lambda code which uploaded the objects into the `service` account (`+` are lines with changes)

```javascript
s3FileProperties = {
  Bucket: destinationBucket,
  Key: entry.entryName,
  ContentLength: fileData.length,
  Body: fileData,
  ContentType: mime.getType(fileName),
  // give target account OAI read access to objects
  + GrantRead: `id=${process.env.SERVICE_CLOUDFRONT_CANONICAL_ID}`,
}
await s3.putObject(s3FileProperties).promise()
```

After making the above change, re-running the pipeline and re-deploying to S3 with updated objected ACLs, we were able to confirm that CloudFront could access the files while the bucket blocked all public access. I made sure this time to invalidate the cache and make a request using both an incognito session and a curl request to verify the changes.

## Lessons

When I worked at Amazon, we had a policy of writing a COE (correction of errors) document whenever an incident occurred that caused customer impact. This was one of my favorite processes at the company and there was even a mailing list where you could subscribe to COEs from other teams. COEs were blameless and as the name hinted at, was about figuring out what happened and how to prevent it from happening the next time (vs finger pointing). At Amazon, we concluded each COE with lessons learned and action items.

Some lessons from this incident:

- validate CloudFront changes by using a clean browser session
- S3 permissions are complicated and make sure to check all of them when debugging a bucket issue
- when you see hoof prints, think horses not zebras

Action items for next time:
- [x] publish a blog about the incident to share learnings
- [x] update Thence playbooks and checklists (these are checklists and tests I keep to speed up AWS investigations)
- [ ] create an open source guide for IAM permissions (this has been a project that I've been wanting to do for a while now because IAM can be such a harrowing topic, more on this in a later update)

## Parting Thoughts

Mistakes suck but they are also the most effective way (I've found) to learn and improve. Even better if you can learn from someone else's mistakes. If you would like to stay up to date on my mistakes, thoughts and other AWS related topics, feel free to subscribe to this blog via [mailing list](https://mailchi.mp/e25c38bdb8d2/thence). There is absolutely no spam, just 1-2 articles a month, delivered to your inbox eventually consistent. That's all for now, and until next time, may you be well and your buckets secure.
