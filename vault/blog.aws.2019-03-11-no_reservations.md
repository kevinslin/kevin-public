---
id: 8fb64753-da7c-42f4-8b45-8879a4e59357
title: 'No Reservations: A Definite Guide to EC2 Reserved Instances'
desc: ''
updated: 1609269301580
created: 1596416622313
tags:
  - ec2
  - ri
  - cost_savings
date: '2019-03-11'
categories: aws
category: aws
toc: true
header:
  teaser: /assets/images/20190312/header.jpeg
excerpt: >-
  EC2 Reserved Instances (RIs) are probably the most effective way of reducing
  your AWS bill by as much as 75% but are underutilized because of perceived
  complexity and lock in. We explore what RIs are all about and what you want to
  consider when purchasing RIs.
layout: single
---

!["Reserved instance representation"](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/header.jpeg)

EC2 Reserved Instances (RIs) provide steep discounts (up to 75%) to the hourly cost of running an EC2 instance in exchange for an upfront commitment to using a particular instance configuration for either a 1 year or 3 year term. In addition to the billing discount, certain RIs also offer capacity reservations which means EC2 will set aside capacity for your indicated instance configuration. Best of all, RI discounts are automatically applied to existing instances which means you can get cost savings without having to make changes to your infrastructure.

For most companies of any size, compute, and specifically EC2, will often be the largest contributor to the annual bill (followed by storage, followed by [not using VPC endpoints](https://www.reddit.com/r/aws/comments/7obna5/10_tb_bill_for_nat_traffic_with_just_a_few_ec2s/)).

EC2 also happens to be the area where companies can reap the biggest savings. Though RI purchases are the "easiest" way to realize said savings, most clients I work with underutilize RIs because of perceived complexity and lock in - complexity because understanding RI purchases can feel like getting a college degree and lock in because of the upfront commitment.

While there is no denying the complexity, the degree of lock in has become much better now that RIs have flexibility for instance size, AZ, and AWS accounts. With the addition of convertible RIs (RIs that you can exchange for other RIs) and the RI marketplace (a place where you can sell the remaining term on your RIs), you now have full control of how committed you want to be while using RIs. Because of this, anyone that runs persistent EC2 based workloads will benefit from purchasing RIs of some sort. The only question is how to choose from what's out there.

This article attempts to answer this age old question. We will go over everything you never wanted to know about RIs, including what each of the different purchasing options actually mean, all the different ways a reservation can get applied, and specific strategies best suited for each particular RI offering.

If you want to skip ahead to the conclusion, you can do so by jumping to the [takeaways](#takeaways). Otherwise, read on.

## RIs = Billing

![EC2 Bill representation](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/conv-bill.jpeg)

The first thing to understand about RIs is that despite the name, they aren't actual instances (well, except for scheduled instances but like 99% of AWS customers, we're going to pretend they don't exist for now). Instead, RIs are a billing concept. When you purchase an RI, AWS will automatically apply a discount to your EC2 bill for an instance that matches the configuration of your RI.

For example, say you are running two t3.large instances in production. You purchase one standard 3 year upfront RI for a single t3.large. AWS will automatically apply the new discounted RI price to one t3.large. The remaining t3.large will continue to be billed at the on demand price.

| t3.large, on demand\* | t3.large, 3 year upfront reserved | % savings |
| --------------------- | --------------------------------- | --------- |
| \$0.0832              | \$0.0313                          | 38%       |

AWS Bill at the end of the month:

| instance | hourly rate | monthly |
| -------- | ----------- | ------- |
| t3.large | \$0.0832    | \$59.90 |
| t3.large | \$0.0313    | \$22.54 |

Total: \$82.44

\*above prices are the quoted hourly prices for us-west-2

## RI Options

When purchasing RIs, there are many options to select upfront:

- payment options (no upfront, partial upfront, all upfront)
- term (1 year or 3 year)
- RI attributes (platform, tenancy, instance size, instance family, etc)
- scope (regional or zonal)
- offering class (standard, convertible, scheduled)

Note that some options like "scope" are not even selectable from the console when first purchasing RIs.

## Payment Options & Term Length

When purchasing RIs, you can select between three payment options:

- no upfront: pay nothing upfront but a fixed monthly fee in addition to the discounted on demand fee
- partial upfront: pay a little upfront and a cheaper fixed monthly fee in addition to the discounted on demand fee
- all upfront: pay a lot upfront and no fixed monthly fee in addition to the discounted on demand fee

In addition to upfront payment, you must specify a term length at the time of purchase of either one or three years.

The discount you get varies by payment option and term length and is proportional to your upfront payment and the length of your term.

For example, lets say you wanted to buy a standard RI for a c5.xlarge in PDX (for all subsequent examples, assume PDX as the implicit region). The following is a chart of expected savings for different payment/terms.

| payment            | upfront payment | monthly | effective hourly\* | 1mo savings | % saving |
| ------------------ | --------------- | ------- | ------------------ | ----------- | -------- |
| on demand          | \$0             | \$0     | \$0.17             | \$122.4     | 0.00%    |
| 1y/no upfront      | \$0             | \$78.11 | \$0.107            | \$45.36     | 37.06%   |
| 1y/partial upfront | \$447.00        | \$37.23 | \$0.102027         | \$48.94     | 39.98%   |
| 1y/all upfront     | \$876.00        | \$0     | \$0.10             | \$50.40     | 41%      |
| 3y/no upfront      | \$0             | \$51.83 | \$0.071            | \$71.28     | 58%      |
| 3y/partial upfront | \$867.00        | \$24.09 | \$0.066            | \$74.88     | 58%      |
| 3y/all upfront     | \$1,629.00      | \$0     | \$0.06             | \$79.20     | 63.54%   |

\*effective hourly normalizes by upfront payment and monthly payment

By using 3y full upfront standard RIs, you can save as much as 63.5% off your AWS bill. General rule of thumb is the bigger your commitment, the more your savings.

## Instance Attributes

RIs have a lot of attributes to choose from. Here are the ones you will need to specify at the time of purchase. Note that unless you are buying Convertible RIs, these attributes (excluding instance size) are fixed and cannot be changed after purchase.

- Platform: eg. Linux, RHEL, Windows
  - Linux is the most flexible (required for instance size flexibility) and most cost effective, windows is most expensive and restrictive
- Tenancy: Default vs Dedicated
  - Default tenancy is shared (depending on your instance size, other customers might be running instances on the same hardware). Dedicated tenancy involves reserving dedicated hardware for only your instances. More expensive. Completely separate on demand and reserved instance pricing
- Instance Family: eg. t2 vs t3
  - When you buy an RI, you are locked in to the instance family that you select. You can avoid this by getting Convertible RIs or by selling your RIs in the RI marketplace (both topics addressed later)
- Instance Size: eg. c5.large vs c5.xlarge
  - Instance size flexibility will allow RIs to be applied to running instances that are of the same family, even if they are of a different instance size. Note that this only applies to Linux Standard RIs

## Scope

RIs can be either regional or zonal in scope. When purchasing RIs through the console, the scope is set to regional by default. Regional scoped RIs mean that the billing discount will reply regardless of the AZ that the instance is launched in. Zonal scoped RIs means that you will have to specify a particular AZ with your RI. The RI discount will only go into effect if the instance is launched in the same AZ. To makeup for the restriction, Zonal RIs give you a capacity reservation. This means EC2 will reserve capacity on your behalf to guarantee that an instance that matches your RI attributes will be available in that particular AZ. This is not provided by standard RIs, which only give the billing discount but don't make any guarantees about whether a particular instance configuration is available.

Note that you can change the scope of an RI at any time, so you can always switch from a zonal RI to a Regional RI if you require greater flexibility.

## Offering Class

When purchasing RIs, there are three primary offerings that you can purchase from:

- standard: most restrictive RI, most cost benefit
- convertible: more relaxed, less cost benefit
- scheduled: most esoteric, useful for specific workloads

We will discuss each in depth.

### Standard RI

![Standard RI Representation](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/conv-standard.jpeg)

Standard RIs are the most restrictive RI category but offer the biggest savings. When you buy a standard RI of a particular instance family (eg. t3.\*), you are locked into that instance family and the instance attributes specified at time of purchase. If AWS releases t4's or your workload shifts and you find that you need c5's, you have to purchase new RIs.

### Convertible RI

![Convertible RI Representation](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/conv-convertible.jpeg)

Convertible RI address the problem of RI lockin by allowing you to trade in your existing Convertible RI for another one of equal or greater value. If the value of the new reservation has a greater upfront fee, you pay a one time true-up charge to cover the difference.

For example, say you bought a 1year all upfront c5.xlarge Convertible RI for $1007 upfront.  AWS just came out with the c6 family and you are currently 6 months into your reservation. You want to switch into a 1year all upfront c6.2xlarge RI for the remainder of the year. The upfront cost of c6.2xlarge is $2000. Because you are halfway into your term, you have $503.50 of prorated value left in your reservation. Because you are getting 6 months of the new reservation, the upfront cost would be $1000. The true-up charge in this case is the value of the new reservation minus the prorated value of your current reservation ($1000 - $503.50 = \$496.50).

When you trade in Convertible RIs, you are also allowed to change other attributes of the reservation such as platform and tenancy.

Convertible RIs cannot be sold in the RI marketplace. You are however, able to subdivide and merge existing reservations. For example, two t2.micro reservations can be converted into a single m4.xlarge reservation. Conversely, one m4.xlarge reservation can be split into two m4.large reservations. You can divide and combine your reservations however you like as long as the resulting reservation(s) has an equal or greater value than what you started off with.

In exchange for the flexibility, savings on Convertible RIs are (around 9%) less than what you would get from an equivalent standard reservation. Below is a breakdown of savings for c5.xlarge when purchasing a standard vs convertible reservation

| payment            | standard effective hourly | convertible effective hourly | % saving diff |
| ------------------ | ------------------------- | ---------------------------- | ------------- |
| 1y/no upfront      | \$0.107                   | \$0.123                      | 9%            |
| 1y/partial upfront | \$0.102027                | \$0.118                      | 9%            |
| 1y/all upfront     | \$0.10                    | \$0.115                      | 9%            |
| 3y/no upfront      | \$0.071                   | \$0.082                      | 6%            |
| 3y/partial upfront | \$0.066                   | \$0.076                      | 6%            |
| 3y/all upfront     | \$0.06                    | \$0.074                      | 8%            |

### Scheduled RIs

![Scheduled RI Representation](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/conv-bus.jpeg)

Scheduled RIs are the least commonly used reservations. They let you reserve capacity for a specified time interval on a daily, weekly or monthly schedule. Reservations also come with a capacity reservation. Scheduled RIs are only available in limited regions (N. Virginia, Oregon and Ireland) and instance types (C3, C4, M4, and R3). Unlike regular RIs, scheduled RIs are special instance types that EC2 set asides. That means you can only purchase scheduled RIs of a certain schedule if EC2 happens to have it available. After purchase, the discounts **do not** apply to existing instances but rather, only to the purchased scheduled instances which will need to be manually started during the scheduled window. EC2 will also automatically terminate these instances 3 minutes before your schedule expires.

Scheduled RIs can not be modified and can not be sold. They are only available for 1 year terms. Cost savings for scheduled RIs are 5-10% and depend on whether you schedule falls within peak or off-peak pricing (typically weekday vs weekend pricing). Scheduled RIs require a minimum utilization of at least 1200h per year.

Its really hard to make a case for when scheduled RIs would be a better fit when you already have spot instances for asynchronous batch jobs and capacity reservations to guarantee capacity for given periods (this is even called out in the official [docs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-scheduled-instances.html?shortFooter=true)). Some use cases that are mentioned at the [launch of scheduled RIs ](https://aws.amazon.com/blogs/aws/new-scheduled-reserved-instances/) include a bank running risk calculations every weekday and a phone company doing bill calculations at the start of every month.

As it stands today, it wouldn't surprise me if this class of RIs takes the same journey as [reduced redundancy S3 storage](https://lastweekinaws.com/blog/s3-reduced-redundancy-storage-is-dead.html).

## RI Flexibility

RIs, like everything in AWS, have gained many new features since they were first announced. There are now a multitude of ways in which RI discounts can be applied.

![EC2 Reserved Instance Evolution](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/ri_evolution.jpg)

## Instance Size Flexibility

Back in the day, an RI purchase was tied to a particular instance size and that was it. If you wanted to change sizes, tough luck. This changed in 2017 when EC2 announced [Instance Size Flexibility](https://aws.amazon.com/blogs/aws/new-instance-size-flexibility-for-ec2-reserved-instances/), which allowed you to apply a RI configured for one instance size to another instance of a different size as long as they were in the same instance family. Note that this only applies to Regional RIs for linux instances running with the default tenancy.

All instances are sized using the following normalization factors.

| Normalization Factor | Instance Size |
| -------------------- | ------------- |
| nano                 | 0.25          |
| micro                | 0.5           |
| small                | 1             |
| medium               | 2             |
| large                | 4             |
| xlarge               | 8             |
| 2xlarge              | 16            |
| 4xlarge              | 32            |
| 8xlarge              | 64            |
| 10xlarge             | 80            |
| 16xlarge             | 128           |
| 32xlarge             | 256           |

With instance size flexibility, you could buy a reservation for one c5.xlarge and apply it to 8 c5.small instances instead. Vice versa, you could apply 2 c5.medium RIs to one 1 c5.large instance.

## AZ Flexibility

When buying a regional scoped RI, the discount will apply regardless of the AZ that the instance is running in. This is in contrast to zonally scoped RIs which can only apply the discount if the instance is running in the same AZ.

## Account Flexibility

RI can be shared across linked accounts. Consolidated billing must be enabled for this to take place. This means that an organization can make all their RI purchases in one central account and any team in the organization can now take advantage of that RI purchase. One caveat is that capacity reservations that come from zonally scoped RIs do not flow down to linked accounts.

## But wait, there's more...

Some other properties of RIs that didn't fit in any of the above sections.

## Marketplace

![EC2 RI Marketplace Representation](https://kevinslin-images.s3.us-west-2.amazonaws.com/images/20190312/conv-market.jpeg)

While purchasing a standard RI locks you into a particular instance family and term, all is not lost if you decide to change. This is because you can still sell the remainder of your reservation in the RI marketplace. You must have owned your RI for longer than 30 days and register as a seller in the reserved instance marketplace before selling.

To sell, list the RIs that you want gone as well as the one time fee that you want to charge. You can have EC2 automatically decrement this one time fee by a fixed amount over time.

What gets sold is the remaining term on your RI, rounded down to the nearest month. You are free to continue using your RI capacity until it is sold. Note that AWS does charge a 12% service fee of your upfront price when a sale goes through.

My experience with the RI marketplace is that you shouldn't expect to make much of a profit or score much cost savings when buying or selling. Use it to offload excess RIs for a minimal cost or to buy RIs at shorter term lengths.

## Volume Discounts

RIs have tiered pricing meaning that it gets cheaper the more you spend. These discounts start taking into effect once you hold more than $500,000 in RIs and result in a 5% discount on both upfront and hourly RI payments at the lowest tier.  They go up to 10% once you surpass $4 million in RI payments. If you hit more than \$10 million, you get to contact AWS and negotiate a super secret large customer discount. Note that Convertible RIs do not qualify for the discount though the money you spend on convertible instances count for calculating whether you've hit a particular volume tier.

## Capacity Reservation

When you create a Standard or Convertible RI with zonal scope, you automatically create a capacity reservation as well - that is to say EC2 will set aside that capacity and make sure it's available for your use. The limitation is that you are restricted in applying your RI discount to a particular AZ and that linked accounts do not get the benefit of the capacity reservation.

One way to get around both issues is to directly purchase [capacity reservations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html) instead. EC2 Capacity reservations can be bought separately from RIs. You choose an AZ, instance type, tenancy and desired schedule and EC2 will make sure that capacity is available during that time. Payment works as follows: you pay the normal hourly price on the instance even if it is not running but don't pay anything extra if the instance is running.

For example, if you create a capacity reservation for 10 c5.xlarge instances and only run 5 c5.xlarge instances, you will be charged the hourly rate for 10 running instances.

Capacity reservations work well with Regional RIs - customers can purchase RIs to get the discount on any instance regardless of AZ and still secure capacity using capacity reservations. RI discounts also apply to unused ec2 capacity reservations, which means in the above example, had you bought 10 Regional RIs for c5.xlarge, you would be charged the RI rate for 10 c5.xlarge.

The best part about capacity reservations is that they require no upfront commitment and can be created and cancelled as needed.

## Limitations

This is not an exhaustive list of limitations but a couple that haven't been mentioned previously. RIs do not apply for dedicated instances. You will need to purchase standalone [dedicated instance reservations](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/) instead. Each AWS account is subject to 20 RI purchases per region per scope (so in total, a new account can make 20 Regional RI purchases and 20 zonal RI purchases) and must contact AWS to raise that limit.

## Takeaways

If you made it all the way to the end of this article, congratulations, as you now know more about RIs then 90% of all people using AWS. In summary, RIs are a fantastic means to save money on EC2 and are flexible enough now that pretty much anyone using an EC2 instance would benefit from having an RI. Some things to remember:

- Standard RI are the most restrictive but yield the greatest savings
- Convertible RIs are the most flexible but yield a lower discount
- Scheduled RIs are very industry specific and its likely that it doesn't apply to you - consider using spot and capacity reservations instead
- RIs can also reserve capacity but at the cost of AZ flexibility - to keep both, consider purchasing capacity reservations with Regional RIs
- if you have excess Standard non-dedicated Linux RIs, they can now be sold in the RI marketplace
- if you are part of an organization, use consolidated billing and a central account to buy RIs for all linked accounts

While we covered EC2 RIs, it's worth keeping in mind that AWS has RIs for many different services, including:

- RDS: reserve specific instance types
- DynamoDB: reserve Write and Read capacity
- Cloudfront: commit to min monthly usage level (based on volume of data transfer)
- ElasticCache: reserve node type and pay upfront and hourly fees
- Elastic Map Reduce: reserve specific instance types
- Redshift: reserve specific instance types

Know that the way AWS handles reserved capacity is pretty similar across services - the bigger the commitment, the more the savings. Also know that like with prescription drugs, restrictions may apply and you should read the fine print of each reservation offering to understand what you are signing up for.

If you'd rather not deal with the nits and grits of RI reservations, considering reserving a consultation at [thence](https://thence.io/) instead. We deal with all the hassle of RI reservations on your behalf as well as filter through hundreds of other cost saving optimizations and only surface savings that will make a difference to your business. Best of all, we never charge more than what you save.

Reserve an [initial consultation](https://calendly.com/thence/meet), with no commitment or upfront fees. This offer is available in all regions for all schedules and applies to organizations of all sizes.
