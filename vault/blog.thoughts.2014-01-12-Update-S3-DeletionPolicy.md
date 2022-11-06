---
id: 057b1ab7-8c72-4117-aee4-fb8234d960e3
title: Update S3 DeletionPolicy in Cloudformation
desc: ''
updated: 1596518032923
created: 1596518032923
tags:
  - aws
  - cloud
  - howto
status: publish
published: true
layout: single
date: '2014-01-12'
---

Found an interesting quirk in Cloudformation when
trying to update an AWS::S3::Bucket resource.

All AWS Cloudformation Resources have the
[DeletionPolicies](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-deletionpolicy.html) attribute which determines the fate of said resource after the
CFN template is deleted. If a resource does not have a deletion policy,
the default behavior is termination.

I ran into a problem  deleting an old
stack when I wanted to retain the s3 buckets and the deployed template didn't have the DeletionPolicy set. Where it any other
resource, I could retroactively [update](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-deletionpolicy.html)
the stack and change the DeletionPolicy. Unfortunately, this technique
works with everything except S3 buckets.

One quirk in the update template workflow is that `DeletionPolicy`
cannot be updated by itself but must accompany some other change that
"add, modify or delete properties" of an existing resource. A fun fact
about the `AWS::S3::Bucket` resource: it cannot be updated after creation. Good news: this does not apply to the DeletionPolicy attribute. Bad news: CFN won't pick up the changes unless another property of
the "immutable" S3 bucket is updated.

The solution: update the
[metadata](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-metadata.html). No explicitly property of the s3 bucket can be updated but since metadata is a common property of all AWS resources, it can. By piggy backing on the metadata change, it's then possible to sneak in a change of the DeletionPolicy.

An example below:

    ...
    {
       "AWSTemplateFormatVersion" : "2010-09-09",
       "Resources" : {
          "myS3Bucket" : {
             "Type" : "AWS::S3::Bucket",
             "Metadata" : { "DummyKey" : "DummyValue"}
             "DeletionPolicy" : "Retain"
          }
       }
    }
    ...

