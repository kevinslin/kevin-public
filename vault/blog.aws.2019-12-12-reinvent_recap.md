---
id: 137d3e99-3ab4-420b-bd14-3ded819e280f
title: 'AWS re:Invent 2019 Recap'
desc: ''
updated: 1627079081809
created: 1596416622318
tags:
  - reInvent
  - serverless
  - networking
  - compute
  - data
  - enterprise
  - tools
date: '2019-12-12'
categories: aws
category: aws
header:
  teaser: /assets/images/20191211-las_vegas.jpg
toc: true
toc_sticky: true
excerpt: 'Everything you need to know about re:Invent 2019'
layout: single
---

!["AWS re:Invent 2019 Las Vegas"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20191211-las_vegas.jpg)

Last week was [re:Invent](https://reinvent.awsevents.com/). This is the annual AWS developer conference that takes place in Las Vegas at the end of the year. Think of it as Christmas, Thanksgiving and New Years all rolled into one.

During re:Invent, AWS unleashes a year's worth of shiny new things. These come in the form of new services (eg. [Amazon Detective](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-amazon-detective/)), major features on existing services (eg. [provisioned concurrency for lambda](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-lambda-announces-provisioned-concurrency/)), and minor service updates (eg. [cognito account recovery method prioritization](https://aws.amazon.com/about-aws/whats-new/2019/11/amazon-cognito-supports-account-recovery-method-prioritization/)).

According to my AWS newsfeed, there were at least eighty such things this year.

!["AWS Recent Announcements"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20191211-recent_announcements.jpg)

Making sense of all the things can be overwhelming but this article can help. We will go over some of the more significant announcements and detail what the things are, why the things might be useful, and what use cases and caveats to watch out for when using the things.

# Serverless

Serverless was a big point of focus this re:Invent. The headline announcement was the launch of provisioned concurrency for Lambda. This was a big deal because [[cold starts|blog.aws.2019-02-04-lambda_cold_start_idle]], extra latency incurred the first time a Lambda is invoked, is the most talked about issue with the platform. Provisioned concurrency mitigates this by allocating a pool of pre-initialized Lambdas. Interesting tidbit - Azure actually announced a [similar feature](https://azure.microsoft.com/en-us/blog/announcing-the-azure-functions-premium-plan-for-enterprise-serverless-workloads/) eight months prior.

While Lambda stole the headlines, I predict that HTTP APIs on API Gateway will make the biggest practical difference for most users. With a 71% price reduction on API Gateway for its most common use case (API proxy), it eliminates the biggest obstacle to adopting this otherwise great service.

- [Lambda Provisioned Concurrency](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-lambda-announces-provisioned-concurrency/)
    - what: "prewarm" desired number of lambda functions
    - why: eliminate [[lambda cold start|blog.aws.2019-02-04-lambda_cold_start_idle]]
    - use case: API servers, enterprise applications, when using .NET or Java in interactive applications
    - caveats: eats into total lambda concurrency limit, pay for lambda even if not invoking it, changes lambda initialization behavior, must be provisioned on explicit lambda version/alias (can't be $LATEST alias)
- [HTTP APIs on API Gateway (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-api-gateway-offers-faster-cheaper-simpler-apis-using-http-apis-preview/)
    - what: (up to 71%) cheaper http requests on API gateway
    - why: API gateway is too expensive for simple API proxy use case
    - use case: any use case where you require an API proxy
    - caveats: doesn't support all features of REST API integration, see comparison [here](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html)
- [Run EKS on Fargate](https://aws.amazon.com/about-aws/whats-new/2019/12/run-serverless-kubernetes-pods-using-amazon-eks-and-aws-fargate/)
    - what: Fargate for EKS
    - why: stop worrying about underlying compute used in container orchestration
    - use case: any workload that runs on EKS today
    - caveats: Fargate more expensive if you have close to full utilization of your cluster, limited region availability
- [Amazon Step Functions Express Workflows](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-aws-step-functions-express-workflows/)
    - what: step functions for short lived workflows
    - why: step functions today is expensive and targeted at long lived workflows
    - use case: anything that requires short-lived workflows at scale (eg. data enrichment of IoT devices)
    - caveats: diff pricing metrics from standard workflow, 5min  execution limit
- [Amazon RDS Proxy (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-rds-proxy-available-in-preview/)
    - what: fully managed db proxy for RDS
    - why: offload resources required for opening, maintaining and pooling connections to db
    - use case: optimize ec2/containers/lambda connecting to RDS
    - caveats: preview only compatible with MySQL in select regions
- [Event Bridge Schema Registry (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-amazon-eventbridge-schema-registry-now-in-preview/)
    - what: central location for event based schemas
    - why: increase scale and resiliency of serverless integrations
    - use case: any use case that requires handling events with pre-defined schema
    - caveats: schema registry is free but schema discovery has a fee

# Security

Security has always been a focus for AWS and this continues to be the case this re:Invent. IAM Access Analyzer was my favorite announcement for re:Invent. It uses [automated reasoning](https://aws.amazon.com/security/provable-security/) to continuously monitor IAM policies for changes. IAM is extremely hard to get right and is a topic I've written about [[extensively in the past|blog.aws.2019-07-17-tale_of_two_buckets]] so anything to make the process easier is a big win for customers. And the cherry on top - it is completely free to use.

- [IAM Access Analyzer](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-aws-identity-and-access-management-access-analyzer/)
    - what: continuously monitor IAM policy for changes
    - why: discover and remediate resource policies that violate security and compliance policies
    - use case: all use cases where security is a concern (it's free so no reason not to turn it on)
    - caveats: currently only works with a few services (S3, KMS, SQS, IAM roles and Lambda)
- [S3 Access Points](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-s3-access-points-manage-data-access-at-scale-shared-data-sets/)
    - what: create access points with custom name, permissions, and endpoints that restrict access to a given S3 bucket
    - why: better access control for shared data sets
    - use case: you have a shared data set and want to give different parties different levels of access to the data
    - caveats: there are now at least [[9 different ways|blog.aws.2019-07-17-tale_of_two_buckets#problem]] of restricting access to a bucket...
- [Amazon Detective (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-amazon-detective/)
    - what: help customers analyze, investigate and identify root cause of security issues
    - why: security is hard, security in the agile cloud era is even harder because of the number of event sources, moving parts and continuous updates
    - use case: conducting infosec on aws for any non-trivial service
    - caveats: should be enabled as master in same account that manages master GuardDuty and Security Hub account (if enabled)

# Compute

Compute is the engine that drives AWS - any improvements here tend to improve AWS as a whole. I would keep an eye out for AWS's graviton processors, custom ARM based chips designed by AWS, which are quickly catching up to the performance of traditionally x86 chips at a fraction of the price.

- [AWS Next Generation Graviton Processors (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/announcing-new-amazon-ec2-m6g-c6g-and-r6g-instances-powered-by-next-generation-arm-based-aws-graviton2-processors/)
    - what: updated ARM based compute
    - why: best price/performance ratio for many workloads, especially those that scale out
    - use case: app servers, caches, video encoding, etc
    - caveats: if you are currently running on Intel/AMD, do your own benchmarking before switching
- [AWS Compute Optimizer](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-aws-compute-optimizer/)
    - what: help right size your ec2 instance using ml and cloudwatch
    - why: AWS has hundreds of instance types, choosing the right one for your use case can be difficult...
    - use case: rightsize ec2
    - caveats: uses cloudwatch metrics to make recommendations(which doesn't measure memory consumption without installing a custom agent) and works only for certain instance families
- [Inf1 Instance](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-amazon-ec2-inf1-instances-high-performance-and-the-lowest-cost-machine-learning-inference-in-the-cloud/)
    - what: ML optimized EC2 instance
    - why: ML
    - use case: ML
    - caveats: only available in us-east-1 and us-west-2 at this moment, not currently supported by sagemaker (support coming soon)
- [Fargate Spot](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-launches-fargate-spot-save-up-to-70-for-fault-tolerant-applications/)
    - what: Fargate now supports spot
    - why: up to 70% discount on compute
    - use case: fault-tolerant workloads (eg. batch processing)
    - caveats: no integration with spot specific features like [spot blocks](https://aws.amazon.com/blogs/aws/new-ec2-spot-blocks-for-defined-duration-workloads/)

# Networking

I still remember the days when EC2 ran on a single flat network (EC2 Classic). The introduction of VPC changed this by logically isolating customers in their own private network, greatly increasing security at the cost of simplicity. Ever since then, AWS networking has been hard at work trying to make networking as seamless as it was in the old days.  Transit gateway and inter region peering comes at the end of a long list of releases (vpc peering, NAT gateways, vpc endpoints, classic link, etc) that help users re-create the illusion of a single flat network.

The other trend in AWS networking is latency reduction. Last year, AWS announced its [Global Accelerator](https://aws.amazon.com/about-aws/whats-new/2018/11/introducing-aws-global-accelerator/), which allowed users to use AWS's private global network to improve performance. The problem is that the speed of light is still a fundamental limitation of any global network. This is why I was excited to see the announcement of new edge networks like Local Zones and Wavelength. AWS Wavelength especially could be game changing in a few years when 5G becomes more than a spec and marketing hype, with the potential of single digit millisecond latencies anywhere a cell tower is available.

- [Transit Gateway Inter Region Peering](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-transit-gateway-supports-inter-region-peering/)
    - what: support peering with networks in different regions
    - why: build global networks
    - use case: build unified network, improve network latency, keep network traffic from public internet
    - caveats: not available in all transit gateway regions
- [AWS Wavelength (preview)](https://aws.amazon.com/wavelength/)
    - what: aws services embedded within the data center of telecommunication providers
    - why: single digit ms latencies for latency sensitive edge devices
    - use cases: games, ml, AR/VR
    - caveats: only support limited providers, regions, and services
- [VPC Ingress Routing](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-vpc-ingress-routing-insert-virtual-appliances-forwarding-path-vpc-traffic/)
    - what: associate route tables with virtual appliances
    - why: apply custom network filtering logic before accepting packets into your vpc
    - use case: security & compliance
    - caveats: only applies for traffic within specific vpc (eg. attached transit gateway doesn't count), doesn't work with route propagation
- [AWS Local Zone in LA](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-aws-local-zone-in-los-angeles-ca/)
    - what: new region type with limited services near big urban centers
    - why: single digit ms latencies for select locations
    - use cases: anything that has tight latency requirements in LA
    - caveats: restricted to LA and limited AWS services
- [36% Faster EBS on Nitro](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-ec2-nitro-system-based-instances-now-support-36-faster-amazon-ebs-optimized-instance-performance/)
    - what: 36% increase in instance bandwidth for new instance families
    - why: faster transfer of data on EBS (eg. cats on the internet)
    - use case: anything that is bandwidth bound on EBS (up to 19 Gbps)
    - caveats: only support nitro compatible ebs-optimized instances

# Databases

AWS was light on new database announcements this year but it might be because they already host every sort of database imaginable. I'm excited about the Elasticsearch Ultrawarm announcement for the same reason that HTTP APIs on API Gateway excite me - it addresses a very common use case and can make a material difference in cloud spend for a lot of users.

- [Elasticsearch UltraWarm (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-announces-ultrawarm-preview-for-amazon-elasticsearch-service/)
    - what: 90% reduction of Elasticsearch storage cost for select use cases
    - why: Elasticsearch storage is expensive
    - use case: store and analyze older data that is used less frequently
    - caveats: can only be added for new domains, cannot be disabled after enabled, can only be enabled if deployed across 3 azs (limitations in place for preview, GA might differ)
- [Redshift Federated Query (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/amazon-redshift-federated-query-preview/)
    - what: query data from RDS in Redshift
    - why: enhance output of Redshift data with RDS data
    - use case: more comprehensive business intelligence reports
    - caveats: only works with select versions of RDS PostgreSQL
- [Cassandra (preview)](https://aws.amazon.com/about-aws/whats-new/2019/12/announcing-amazon-managed-apache-cassandra-service-now-in-preview/)
    - what: managed Cassandra service
    - why: managing Cassandra is difficult at scale
    - use case: applications that are using Cassandra
    - caveats: [functional differences from Apache Cassandra](https://docs.aws.amazon.com/mcs/latest/devguide/functional-differences.html)

# Enterprise

While AWS has traditionally been cloud centric, it has increasingly offered more hybrid functionality over the years to cater to its enterprise customers.  AWS Outposts is the best example of this - if enterprise won't adopt the cloud, AWS will bring the cloud to them. Outposts is also a blatant example of double dipping - it was announced as [a preview](https://aws.amazon.com/about-aws/whats-new/2018/11/announcing-aws-outposts/) during the 2018 re:Invent and was announced again this year for general availability.

- [AWS Outpost](https://aws.amazon.com/about-aws/whats-new/2019/12/announcing-general-availability-of-aws-outposts/)
    - what: run limited aws services in on-prem data centers
    - why: compliance, security, and latency
    - use case: hybrid cloud
    - caveats: require enterprise support plan, limited AWS service support

# Developer Tools

When I worked at AWS, my favorite part of the job was the access to talks and articles published by some of the best developers that I knew. Therefore I'm really excited to see that material made available to the public via the Builders Library. Pro tip: if you see an article written by Becky Weiss, Colm MacCarthaigh, or Marc Brooker, do yourself a favor and read it end to end!

- [Amazon Builders Library](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-amazon-builders-library/)
    - what: documented strategies and designs on how Amazon builds, delivers and operates its own services
    - why: share best practices
    - use case: design for scale, availability, and scalability
    - caveats: your mind might be blown
- [Code Guru](https://aws.amazon.com/about-aws/whats-new/2019/12/aws-announces-amazon-codeguru-for-automated-code-reviews-and-application-performance-recommendations/)
    - what: ML based code reviews and code profiling
    - why: help catch code issues
    - use case: additional review of code
    - caveats: profiler only supports java applications, pricing is based on newlines


# In Closing

Another year, another re:Invent.

It's always hard to nail down specific themes from the conference because of the broad range of announcements and product categories but if I were to generalize, it would be the following:

1. Continued improvements in fundamentals (compute, networking and databases)
2. Focus on serverless, security, developer tools, ML and enterprise
3. Focus on the integration, operation and management of multiple services, regions, and accounts

A trend in re:Invent's of late is for more and more services to launch in preview or with limited availability. One can hope this will give them time to work out the kinks and add cloudformation support before the general release.

In the meantime, hope that this recap has helped make sense of a few things and sparked ideas for how they can be used in your own services.
