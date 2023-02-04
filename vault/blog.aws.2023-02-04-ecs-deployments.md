---
id: hx4gi4421yfamzv265imwlq
updated: 1675532165903
created: 1675532165903
title: ECS Deployment with Github Actions
desc: ''
tags: [aws]
date: '2023-02-04'
excerpt: |
     Thoughts on recent ECS deployment
---

Some thoughts on a recent hiccup when trying to deploy a private ECR image to ECS using github actions. 

The guide that I ended up following: https://github.com/aws-actions/amazon-ecs-deploy-task-definition
The policy I ended up with (using CDK):
```ts
const deployPolicyJson = {
  Version: "2012-10-17",
  Statement: [
    // -------------------------- This was mentioned in the github guide
    {
      Sid: "RegisterTaskDefinition",
      Effect: "Allow",
      Action: ["ecs:RegisterTaskDefinition"],
      Resource: "*",
    },
    {
      Sid: "PassRolesInTaskDefinition",
      Effect: "Allow",
      Action: ["iam:PassRole"],
      Resource: [
        `arn:aws:iam::${AWS_ACCOUNT_ID}:role/${taskRoleName}`,
        `arn:aws:iam::${AWS_ACCOUNT_ID}:role/${executionRoleName}`,
      ],
    },
    {
      Sid: "DeployService",
      Effect: "Allow",
      Action: ["ecs:UpdateService", "ecs:DescribeServices"],
      Resource: [
        `arn:aws:ecs:${AWS_REGION}:${AWS_ACCOUNT_ID}:service/${clusterName}/${serviceName}`,
      ],
    },
    // -------------------------- These were permissions I ended up figuring out through trial and error
    {
      // everything else
      Sid: "Bucket",
      Effect: "Allow",
      Action: [
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload",
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage",
      ],
      Resource: opts.repos.map(ent => `arn:aws:ecr:${AWS_REGION}:${AWS_ACCOUNT_ID}:repository/${ent.repositoryName}`)
    },
  ],
};
```

In retrospect, the scope of ecs-deploy does say it is the minimal set of permissions to deploy an image. If I was deploying a public docker image, it would have been enough. Because we were using a private ECR image, there were additional permissions that were necessary (which in the process of writing this, I was able to find inside of the ECR docs: https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-push.html)

As an end user, this is where working with AWS services starts to feel like pulling teeth. 

I understand that its impossible for a service team to account for the long tail of use cases that anyone might do with a given service. 
That being said, why do the docs explicitly targeted for **deploying images to ECS** not cover, or even hint at, the detail for what is required to deploy said (private) image? 

Well, this message turned into more of a rant but hopefully it comes out constructive. I do appreciate the challenge involved in communicating the nuisance of something as complicated as ECS. 

Taking a step back, it also makes me feel as if IAM permissions are just fundamentally the wrong level of abstraction for most people to deal with. What assembly code was before C, that's how IAM permissions feel like. I think the CDK does a good job at setting sane defaults when provisioning resources and auto-adding permissions when necessary but ultimately still feels like a bandage fix. I don't actually know what the right thing is. 