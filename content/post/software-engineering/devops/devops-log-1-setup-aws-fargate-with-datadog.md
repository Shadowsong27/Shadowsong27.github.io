---
title: "DevOps Log 1: Setup DataDog and APM in AWS Fargate"
thumbnailImagePosition: "left"
thumbnailImage: https://cdn.aimconsulting.com/wp-content/uploads/2018/07/aws-fargate-v2.png
coverImage: https://cdn.aimconsulting.com/wp-content/uploads/2018/07/aws-fargate-v2.png
metaAlignment: center
coverMeta: out
date: 2019-07-16
categories:
- software-engineering
- dev-ops
tags:
- AWS
- Fargate
- Monitoring
---

Recently I am migrating some microservices and streaming pipelines to AWS Fargate. 
This article mainly covers the issues, pitfalls and other useful notes 
when I was deploying a web backend with DataDog.
<!--more-->

Previously we set up DataDog on a standard EC2 machine, together with the  web application.

This article would omit stuff that are clearly mentioned and explained in the official
DataDog documentations. It would only annotate on the issues I encountered during setup.

# Resources/Terms Explanation
AWS Fargate: AWS managed container service, allows you to run your docker 
without worrying about underlying infrastructure.

DataDog: A monitoring tool for application, supports tracing of requests 
and calls in various resources

# Breakdown Steps

### Preparation
1. Push your application container(s) into ECR or any container registry (if you have not done so)
2. Generate API Key in DataDog
3. Understand the basics of docker, how docker communicate with each other in a network

### Create Task Definition

Setup the Task Definition normally, configure your Task Execution Role 
based on your application’s need. The tricky part is the container setup, 
since we want to have the DataDog Agent and application in different containers, 
we need to configure both of them so that they could talk to each other.

It is stated in the official docs that:

> To monitor your ECS Fargate tasks with Datadog, run the Agent as a container in same task definition as your application. To collect metrics with Datadog, each task definition should include a Datadog Agent container in addition to the application containers.

Basically this means we need to have at least two containers in the Task Definition setup.

![pic](/img/aws-fargate-datadog-1.png)

### Configure DataDog Agent Container

This is technically similar to set up the `datadog-agent.yaml` config file on a EC2 instance for DataDog, except on EC2 everything is filled with many options and you just need to uncomment stuff on a need basis. Here we need to configure everything using Environment Variable and Docker Label.

Integration Setup tutorial could be found here: [Integration Setup for ECS Fargate](https://docs.datadoghq.com/integrations/faq/integration-setup-ecs-fargate/?tab=rediswebui)

Environment Variable configurations:
Details of each ENVs used to configure the agent could be checked here: 
[Docker Agent](https://docs.datadoghq.com/agent/docker/?tab=standard)

Key    | Value
--------|------
DD_API_KEY | YOUR_API_KEY
DD_APM_ENABLED | true
DD_APM_IGNORE_RESOURCES | YOUR_IGNORED_RESOURCES
DD_APM_NON_LOCAL_TRAFFIC | true
DD_APM_RECEIVER_PORT | 8126
DD_DOGSTATSD_NON_LOCAL_TRAFFIC | true
DD_DOGSTATSD_PORT | 8125
ECS_FARGATE | true

You will also need to open the ports configurations in containers, by default it is using `8126` to receive traces. Since we are not running the agent and the application on the same machine, `NON_LOCAL_TRAFFIC` related ENVs needs to be set as `true`.

[APM & Distributed Tracing](https://docs.datadoghq.com/tracing/#setting-up-apm)

Open the following ports as stated in docs:

Port    | Protocol
--------|------
8126 | TCP
8125 | UDP 


### Configure Application Container

The configuration on the application container is much simpler. It will need to be able to send traces to the agent container. So we need to define two extra ENVs:

Key    | Value
--------|------
DD_AGENT_HOST | localhost
DD_TRACE_AGENT_PORT | 8126

The `localhost` is not written anywhere on official docs. I originally use `datadog-agent` , the name of the agent container, after that I see the logs saying:

```
2019-07-16 07:57:03,286 : ERROR : Failed to send traces to Datadog Agent at datadog-agent:8126: [Errno -2] Name does not resolve
```

This tells us that the hostname in this case is not `datadog-agent`, which should work if we are using `docker-compose` by just stating the `service` name. Because of this I start to read more deeply in the Fargate docs and I found this here:  [https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) 

> When a task is started with the `awsvpc` network mode, … This means that all containers in a task are addressable by the IP addresses of the ENI, and they can communicate with each other over the `localhost`interface.

Well then I tried `localhost` and it worked. 

When I go back and try to understand the whole thing, I associated this with my effort to setup a docker Postgres Together with other applications. The IP address of the database to the application is never `localhost`, it is rather the assigned IP address by the docker host, but in Fargate because of ENI they seem to solve this problem.

### Configure Application Code

I am using Python, so basically it is configure the tracer during application initialisation, nothing much to be elaborated here. Something similar to the following code would do:

```
import os
from ddtrace import tracer

tracer.configure(
    hostname=os.environ['DD_AGENT_HOST'],
    port=os.environ['DD_TRACE_AGENT_PORT'],
)
```


# Conclusion
Enable logs for the DataDog agents help with the debugging. It is advised to leave it on for the first few times, and disabled it later on if you find it unnecessary.

I would consider myself pretty weak when it comes networks (never studied in an academic sense), but with constant exposure to things like docker-compose and managed services, you focus much more on the connection of resources rather than the provision of them. This is pretty useful.
