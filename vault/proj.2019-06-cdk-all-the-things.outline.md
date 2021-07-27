---
id: l1yYptFpYE48UBDG
title: Outline
desc: ''
updated: 1627421641183
created: 1627421641183
noindex: true
---


## Summary
- how I started using it
- goal of article
- overview of cdk
- usage experience
- verdict
    - love
    - good
    - edges
- takeawy
- next

## Scatch
AWS CDK All of the Things: How I learned to Keep Calm and Trust the CDK

Building Production AWS Infrastructure

- How I learned to keep calm and trust the CDK
- How I 10x my devops
- DevOps with typesetting

How I learned to trust the CDK

- overview of cdk
    - aws version of terraform
    - infrastructure as code, instead of config
    - join a crowded field
        - 3rd party
        - even aws has a lot
    - writing config is painful, xml templating painful
    - we will do anything to get out of it
- how I started using it
    - meaning to ever since came out
    - recent ci/cd pipelines, decided to do it
- usage experience
    - used in a few client projects, deploying ci/cd pipelines
    - i used to do binary and switch to assembly
    - good:
        - higher level constructs
            - code deploy example
        - support for lambda
            - lambda eample
        - fantastic tooling
            - cdk commands
        - easy conditionals
            - conditionals were never meant for templating and result is always ugly
        - cdk is just code, everything on table
            - loops, conditionls, testing, etc
        - test code
    - bad
        - breaking changes
            - 0.31 -> 0.33
        - fall back to lower level constructs
        - bugs/unexpected behavior
        - what documentation
    - end of the day, generates cloudformation which is great
    - have cake and eat it to
- verdict
    - love
    - super great
    - edges
    - great community
- takeawy
    - cdk is the best way of going forward
    - all the benefits of terraform but great if you are all in on aws and work natively with cloudformation
    - write code
- next
    - people interested, doing a cdk learnings & best practices if useful

- like console
    - sane defaults

- have cake and eat
    - write in code
    - have cloudformation genreate config and compare diff

- templating
    - cloudformation
    - amplify
    - sam

    - terraform
    - troposphere
    - Pulumi

- serverless
    - serverless
    - sam
    - cdk

## Other
- examples of delight
- best practices
