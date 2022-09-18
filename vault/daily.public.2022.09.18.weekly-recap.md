---
id: ap5f9kshwnphhxcc3yuw709
title: '2022-09-18'
desc: ''
updated: 1663534032252
created: 1663533568765
traitIds:
  - daily-public
---

Two years into the pandemic and finally caught covid. First two days were rough, mainly because of difficulty sleeping due to caughts but was able to recover the remaineder of the week. Remarkable how isolating spending two weeks in the house cam be. 

On the other hand, this has given me plenty of time to work on  dendron's managed publishing infrastructure. 
It's a gluttony of choices. Take compute.  Want to run a docker container on AWS? 

Choose one (or more):
- Lambda with docker
- ECS 
- ECS, fargate
- EKS
- EBS
- EC2/Autoscaling
- Lightsail (?): need to confirm but don't see why not
- AWS Code Build (AKA the best managed docker service that you've never heard of)

Had a lambda prototype working but deploying lambda code in VPC just takes a long time so sticking with regular EC2 + Autoscaling while we're building up the service and keep lambda/fargate as a possibility when we scale up. 

