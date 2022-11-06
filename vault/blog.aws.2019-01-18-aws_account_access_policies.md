---
id: 661b56e7-23bc-4955-8b7f-9c7af18bc2e3
title: Giving Third Parties Limited Access to your AWS Account
desc: ''
updated: 1608778705517
created: 1596416622309
tags:
  - howto
  - security
  - iam
date: '2019-01-18'
categories: aws
category: aws
header:
  teaser: /assets/images/20190121-access_lock.jpg
toc: true
toc_sticky: true
excerpt: >-
  An exploration of various AWS managed read-only IAM policies. Understand what
  they do and how to apply them to your account the next time you have a third
  party access your AWS account.
layout: single
---

!["AWS Access Control represented by lock picture"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-access_lock.jpg)

Whenever I'm working with a new client, one of the first things we talk about is access. Most clients have the majority of their business running on top of AWS and don't feel comfortable (nor would I recommend) that they give a consultant complete access on day one. That being said, said consultant does need minimal access in order to advise, whether this is on security, architecture, cost, or any number of reasons. This is when we turn to IAM.

# IAM and Managed Policies

[IAM](https://aws.amazon.com/iam/) (Identity Access Management) is one of the longest running and most comprehensive service in AWS (and arguably the most feature rich IAM service of any cloud provider). With IAM, users can specify precisely the level of access any given entity will have to their AWS resources. You can narrow down permissions to single actions in specific services (eg. Bob can only call `s3:GetLifecycleConfiguration` on bucket `Foo`) as well as use wildcards to specify a whole range of permissions (eg. Bob can call `s3:List*` actions on all buckets starting with `Foo*`). IAM is an incredibly flexible and powerful way of only providing exactly the permissions that are actually necessary but comes at the cost of being verbose and tricky to get right. AWS is aware of this issue and offers [managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) that deal with some common use cases. The advantage of using managed policies is that they are implemented and managed by AWS security engineers and are regularly updated to include the latest services and features.

Today, we're going to talk about three of these managed policies which I recommend my clients use when giving third parties access. They are as follows:

- ViewOnlyAccess: This policy grants permissions to view resources and basic metadata across all AWS services.
    - great for initial audit since it allows the user to see all aws resources and metadata but not the actual business and customer data
    - eg: will allow user to see EC2 instances, RDS instance, S3 buckets and objects (only the names but not the contents, configs), etc

- SecurityAudit: This policy grants access to read security configuration metadata. It is useful for software that audits the configuration of an AWS account.
    - great for security assessments - very similar to `ViewOnlyAccess` but grants additional access to configuration/policy while limiting certain view operations
    - comparison: `ViewOnlyAccess` allow users to perform `glacier:List*` operations but no `glacier:Get*` operations whereas `SecurityAudit` allows `glacier:ListVault` and limited `GET` operations like `glacier:GetVaultAccessPolicy`. Similar trade-offs are made throughout other AWS services

- ReadOnlyAccess: This policy provides read-only access to AWS services and resources.
    - great for a deeper audit - useful for troubleshooting issues because users can read data out of aws resources like S3 and Dynamo
    - comparison: all the permissions of `ViewOnlyAccess` with additional permissions to read resource contents as well as the metadata
    - note that anyone with this policy can **READ AND DOWNLOAD DATA from S3, Dynamo and your other aws services** - for most cases, `ViewOnlyAccess` is sufficient

While the above mentioned security policies are updated to include the latest AWS services, the rate that AWS releases changes means that these even these policies can be out of date. As of this writing, here are some shortcomings I found:

- ViewOnlyAccess:
    - missing permissions for GuardDuty, Alexa4Business, Cloud9, Comprehend, etc
- SecurityAudit:
    - missing permissions for Inspector, Lex, Lightsail, Polly, Simple Work Flow, WorkDocs, etc
    - a lot of the missing permissions here center around high level services like Lex and WorkDocs so that might be why they're excluded but it's surprising that Inspector isn't in here as it is a security assessment service
- ReadOnlyAccess:
    - missing permissions for AppMesh, Chime, License Manager, MediaConnect, Quicksight, Resource Access Manager, RoboMaker, SecurityHub, AWSMarketPlace, etc
    - most of the missing permissions are with recently launched services (eg. RoboMaker, AppMesh), but the AWSMarketPlace is surprising since `ViewOnlyAccess` has the `aws-marketplace:ViewSubscriptions` permission which makes me suspect that this is an oversight

The takeaway here is that these managed policies are a good starting point to give a third party access limited access to your AWS account. They do miss whitelisting a service here and there but it is much better to whitelist a few services after the fact then realize you've given too many permissions and blacklist.

Now that we've gone over a few managed policies, below are instructions on how to apply them to your own AWS Account. Note that the instructions are for applying the `ViewOnlyAccess` policy but the same steps apply for the other managed policies.

# Granting ViewOnlyAccess to your AWS Account - Console Instructions

## Creating an AWS User
- Navigate to the user tab under IAM by going to [https://console.aws.amazon.com/iam/home#/users](https://console.aws.amazon.com/iam/home#/users) and click `Add User`

!["Add User to IAM console"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-1-add_user.jpg )

- Set a name for the user and enable both Programatic and Console Access. Then click to `Next: Permissions`

!["Enable AWS console access"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-2-user_details.jpg )

- Click `Attach existing policy directly` and look for `ViewOnlyAccessPolicy`. Select the policy before clicking `Next: Tags`

!["Attach existing policies directly"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-3-add_existing_policy.jpg )
!["ViewOnlyAccess Policy"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-4-add_policy.jpg )

- Optionally, add a tag to the project for tracking purposes. Click `Next ` when done.

!["Add IAM Tags"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-5-optional_add_tag.jpg )

- Make sure that everything looks right and click `Create User`
!["Review managed policies"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-6-create_user.jpg )

- Download the csv and send an email to the person you are granting access to with the IAM login link. Send the csv with the credentials along with the email or separately in a different channel of your choice.

![](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-7-download_email.jpg ) %}
!["Access Key Id, Secret Access Key and Password screen"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-8-email.jpg )

## Deleting an AWS User
- After the audit is done, its important to delete the credentials to limit further access (optionally, you can also choose to disable the keys and password but this guide will cover deleting the credential). To do so, go back to the IAM user page [here](https://console.aws.amazon.com/iam/home#/users). Select the user you wish to delete and click `Delete User`. Click `Yes` on the ensuing confirmation box.

!["Send email with Sign-in URL to Audit Only User"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190121-aws_audit-9-delete_user.jpg )

And you're done. Hope this article was informative and feel free to reach out or comment if you have any questions or feedback for this post.

# Granting ViewOnlyAccess to your AWS Account - CLI Instructions

If you want to automate the above process, you can do so by running the following series of AWS CLI commands

## Creating an AWS User

```
aws iam create-user --user-name "ViewOnlyUser" --path "/"
aws iam attach-user-policy --policy-arn "arn:aws:iam::aws:policy/job-function/ViewOnlyAccess" --user-name "ViewOnlyUser"
aws iam attach-user-policy --policy-arn "arn:aws:iam::aws:policy/IAMUserChangePassword" --user-name "ViewOnlyUser"

# do this if you want to enable programatic access for user. this cmd outputs AccessKey & SecretAccessKey. make sure to store this in a safe place
aws iam create-access-key --user-name "ViewOnlyUser"  > /tmp/aws_credentials.json

# do this if you want user to have console access - note that we use openssl to generate the password. you can use your own means of password generation
password=`openssl rand -base64 32`
aws iam create-login-profile --user-name "ViewOnlyUser" --password-reset-required --password $password

# get signin link - note, this uses the jq cli (https://stedolan.github.io/jq/) to parse the json output
signinlink=`aws iam get-user --user-name "ViewOnlyUser" | jq .User.Arn | awk -F : '{print "https://"$5".signin.aws.amazon.com/console"}'`

# use a secure channel to send the following output to a third party
cat /tmp/aws_credentials.json
echo $password
echo $signinlink

```

## Deleting an AWS User
```
aws iam delete-login-profile --user-name ViewOnlyUser
aws iam detach-user-policy --user-name ViewOnlyUser --policy-arn "arn:aws:iam::aws:policy/job-function/ViewOnlyAccess"

# this requires the access key of IAM user - note, this uses the jq cli (https://stedolan.github.io/jq/) to parse the json output
aws iam delete-access-key --user-name "ViewOnlyUser" --access-key-id=`aws iam list-access-keys --user-name "ViewOnlyUser" | jq ".AccessKeyMetadata[0].AccessKeyId"`

aws iam delete-user --user-name ViewOnlyUser

```
